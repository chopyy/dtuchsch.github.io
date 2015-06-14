# Enable real-time capabilities of the mainline kernel on the Raspberry PI
Embedded systems often need hard realtime capabilities for driving actuators or reading out sensor data at a given time. This guideline shows how to apply the realtime kernel from OSADL on a Raspberry PI (RPI) v2. We will go through downloading the sources, cross-compiling the kernel on a PC architecture and finally flashing it on the RPI.

I'm cross-compiling the kernel on an Intel i7 CPU with 8 GB of RAM in Ubuntu as my host system, because it will compile a lot faster than compiling the kernel on the RPI itself. As embedded device I'm using a Raspberry PI v2 Model B.

## Downloading and Patching
There are several possible kernels you can use for building a kernel with PREEMPT RT. The easiest way to get a realtime kernel running on the RPI is to use the raspberry kernel from their GitHub repositories. Another way is to use the Vanilla kernel, but you have to add all extras and the Raspberry firmware by yourself. So, this guide is separated in two main parts. In the first part of this guide I will describe the way for patching and building the realtime kernel with the kernel source from the GitHub repository of Raspberry. In the second part I describe the more generic way to get the Vanilla kernel also running. But there are some more things to do and the work is in progress.

### Raspberry kernel
Download the Raspberry kernel from GitHub:
```shell
cd /usr/src/
mkdir kernels
cd kernels
git clone --depth=1 https://github.com/raspberrypi/linux
```

Look for the kernel version of the kernel. You will find the information within the Makefile and it should look something like:
```
VERSION = 3
PATCHLEVEL = 18
SUBLEVEL = 11
```

So in my case I will need the realtime patch 3.18.11:
```shell
cd /usr/src/kernels
sudo wget https://www.kernel.org/pub/linux/kernel/projects/rt/3.18/older/patch-3.18.11-rt7.patch.xz
```

If your kernel version differs from the one described in this guide, search for the appropriate kernel patch on https://www.kernel.org/pub/linux/kernel/projects/rt/ and download it like described above.

Rename the folder you cloned from GitHub, because we will patch it in the next step:
```shell
cd /usr/src/kernels
sudo mv linux linux-rt
```

Go into your linux-rt directory and patch it with the downloaded RT PREEMPT patch:
```shell
sudo -i
cd /usr/src/kernels/linux-3.18.11-rt/
sudo xz -dc /usr/src/kernels/patch-3.18.11-rt7.patch.xz | patch -p1
```

**Note:** This step has to be executed as root explicitly (sudo -i).

#### Cross Compiler
There is more than one ARM cross compiler for Ubuntu Linux. We will use the provided compiler from the *Raspberry Pi tools section* on GitHub.
```shell
cd /opt/
git clone git://github.com/raspberrypi/tools.git --depth 1
```

#### Configuration
Now comes the exciting part: Configuring the kernel! But before you are able to configure the kernel and activate the realtime capabilities, make sure you installed all necessary dependencies and get the linux kernel ready to compile:
```shell
sudo apt-get install libncurses5-dev
make mrproper
```

Set an environment variable for the prefix of the cross compiler toolchain:
```shell
export CCPREFIX=/opt/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-
```

Make an initial config for the Raspberry build:
```shell
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} bcm2709_defconfig
```

In the same terminal where you exported the environment variable run your first make command to configure your realtime kernel:
```shell
KERNEL=kernel7
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} menuconfig
```

This make target will start up a graphical interface shown below: 

![](gfx/Menuconfig_Start.png)

If you configured the ARM build correctly the Preemption Model of the PREEMPT patch can be found under *Kernel Features* and then **Preemption Model** like shown in the following image:

![](gfx/Preemption_Model_menuconfig_ARM.png)

Choose **Fully Preemptible Kernel (RT)** here.

**Note:** If this option is not available the patch was not successful and something went wrong. Try to patch the downloaded kernel once again.

#### Compiling
You can now compile the kernel on the PC. If you are working on a multi-processor platform enable parallel jobs for a quicker build switching on *-j #n* where #n is the number of parallel jobs that will be used. This should be 1.5 times your number of cores. Using this option makes the build about 48% faster on a modern Intel/AMD processor. Now you can imagine how long it would take to compile the kernel directly on the RPI itself (more than 3 hrs I guess).

For the raspberry kernel simply execute the following commands:
```shell
cd /usr/src/kernels/linux-rt
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} zImage modules dtbs -j5
```

After successful compilation the compiled kernel is located under *arch/arm/boot/*.

#### Flash the compiled kernel
After compiling the kernel we can flash the image onto the RPI. First, plug in your SD card from RPI:
```shell
lsblk
```

You should see something like this:
```shell
NAME
mmcblk0
|-mmcblk0p1
|-mmcblk0p2
```

Mount both drives:
```shell
mkdir mnt/fat32
mkdir mnt/ext4
sudo mount /dev/sdb1 mnt/fat32
sudo mount /dev/sdb2 mnt/ext4
```

Install the modules of the compiled kernel:
```shell
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} INSTALL_MOD_PATH=media/dtuchscherer/13d368bf-6dbf-4751-8ba1-88bed06bef77/ modules_install
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} INSTALL_MOD_PATH=mnt/ext4 modules_install
```

Back up your old kernel and copy the realtime kernel onto the SD card:
```shell
cd /usr/src/kernels/linux-rt
sudo cp /media/dtuchscherer/boot/$KERNEL.img /media/dtuchscherer/boot/$KERNEL-backup.img
sudo scripts/mkknlimg arch/arm/boot/zImage /media/dtuchscherer/boot/$KERNEL-rt.img
sudo cp arch/arm/boot/dts/*.dtb /media/dtuchscherer/boot/
sudo cp arch/arm/boot/dts/overlays/*.dtb* /media/dtuchscherer/boot/overlays/
```

Finally, adjust the config file:
```shell
kernel=kernel-rt.img
```

## Running and Testing
After you transferred the kernel onto the RPI, plug in your SD card and boot the controller. Test if the kernel and the realtime patch was successful:
```shell
uname -a
```

It should show something similiar to this:
```shell
Linux raspberrypi 3.18.14-rt7-v7+ #1 SMP PREEMPT RT Fri Jun 12 12:08:45 CEST 2015 armv7l GNU/Linux
```

There are tools for testing if the patch was successful. Log in to your RPI over SSH and clone the following GitHub repository:
```shell
ssh pi@141.7.28.126
git clone git://git.kernel.org/pub/scm/linux/kernel/git/clrkwllms/rt-tests.git
cd rt-tests
make
```



### Vanilla kernel
**Note:** This work is in progress and will be released later on.
<!--
Download both the kernel as well as the corresponding RT patch. We will work with **kernel version 3.18.11**:
```shell
cd /usr/src/kernels
sudo wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.18.11.tar.xz
sudo wget https://www.kernel.org/pub/linux/kernel/projects/rt/3.18/older/patch-3.18.11-rt7.patch.xz
```

If directory kernels does not exist create it:
```shell
cd /usr/src/
mkdir kernels
```

Unpack the kernel:
```shell
sudo tar -xvJf linux-3.18.11.tar.xz
```

Rename the kernel directory:
```shell
sudo mv linux-3.18.11 linux-3.18.11-rt
```

Change the directory into the unpacked kernel and patch it with RT PREEMPT.
```shell
sudo -i
cd /usr/src/kernels/linux-3.18.11-rt/
sudo xz -dc /usr/src/kernels/patch-3.18.11-rt7.patch.xz | patch -p1
```

## Cross-Compiler
There is more than one ARM cross compilers for Ubuntu Linux. We will use the provided compiler from the *Raspberry Pi tools section* on GitHub.
```shell
cd /opt/
git clone git://github.com/raspberrypi/tools.git --depth 1
```

## Configuration
Before you are able to configure the kernel make sure you installed all necessary dependencies and get the linux kernel ready to compile:
```shell
sudo apt-get install libncurses5-dev
make mrproper
```

We also need to copy the kernel **.config file** from the RPI into the build directory.
If there is a .config in the linux-3.18.11-rt folder file already, back it up. Then we will transfer the RPI .config from the embedded device onto the PC.
```shell
cd /usr/src/kernels/linux-3.18.13-rt
sudo mv .config .config_backup
sudo rsync -avz -e ssh pi@141.7.25.81:/proc/config.gz /usr/src/kernels/linux-3.18.11-rt/.config
```

Set an environment variable for the prefix of the cross compiler toolchain:
```shell
export CCPREFIX=/opt/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-
```

In the same terminal where you exported the environment variable run your first make command to configure your kernel:
```shell
KERNEL=kernel7
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} menuconfig
```

This make target will start up a graphical interface shown below: 

![](gfx/Menuconfig_Start.png)

For PC architectures the realtime settings are under *Processor type and features*. Select the **Preemption Model** and choose **Fully Preemptible Kernel (RT)** like in the screenshot below:
![](gfx/Preemption_Model_menuconfig.png)

If you configured the ARM build correctly the Preemption Model of the PREEMPT patch can be found under *Kernel Features* and then **Preemption Model** like shown in the following image:

![](gfx/Preemption_Model_menuconfig_ARM.png)

Also choose **Fully Preemptible Kernel (RT)** here.

**Note:** If this option is not available the patch was not successful and something went wrong. Try to patch the downloaded kernel once again.

## Compiling
You can now compile the kernel on the PC. If you are working on a multi-processor platform enable parallel jobs for a quicker build switching on *-j #n* where #n is the number of parallel jobs that will be used. This should be 1.5 times your number of cores. Using this option makes the build about 48% faster on a modern Intel/AMD processor. Now you can imagine how long it would take to compile the kernel directly on the RPI itself (more than 3 hrs I guess).
```shell
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} -j5
```

Next we will build the modules
```shell
sudo make ARCH=arm CROSS_COMPILE=${CCPREFIX} -j5 modules
```

After successful compilation the compiled kernel is located under *arch/arm/boot/*.
-->

# References
* http://elinux.org/Raspberry_Pi_Kernel_Compilation
* https://www.raspberrypi.org/documentation/linux/kernel/building.md
* https://www.osadl.org/?id=87

