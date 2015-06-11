# Enable real-time capabilities of the mainline kernel on the Raspberry PI
Embedded systems often need hard realtime capabilities for driving actuators or reading out sensor data at a given time. This guideline shows how to apply the realtime kernel from OSADL on a Raspberry PI (RPI) v2. We will go through downloading the sources, cross-compiling the kernel on a PC architecture and finally flashing it on the RPI.

I'm cross-compiling the kernel on an Intel i7 with 8 GB of RAM in Ubuntu as my host system, because it will compile a lot faster than compiling the kernel on the RPI itself.

## Downloading and Patching

Check the kernel version of your RPI:

``` shell
uname -r
# ...
```

Download both the kernel as well as the corresponding RT patch. We will work with **kernel version 3.18.11**:
``` shell
cd /usr/src/kernels
sudo wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.18.11.tar.xz
sudo wget https://www.kernel.org/pub/linux/kernel/projects/rt/3.18/older/patch-3.18.11-rt7.patch.xz
```

If directory kernels does not exist create it:
``` shell
cd /usr/src/
mkdir kernels
```

Unpack the kernel:
``` shell
sudo tar -xvJf linux-3.18.11.tar.xz
```

Rename the kernel directory:
``` shell
sudo mv linux-3.18.11 linux-3.18.11-rt
```

Change the directory into the unpacked kernel and patch it with the RT PREEMPT patch.
``` shell
sudo -i
cd /usr/src/kernels/linux-3.18.11-rt/
sudo xz -dc /usr/src/kernels/patch-3.18.11-rt7.patch.xz | patch -p1
```

Before you are able to configure the kernel make sure you installed all necessary dependencies:
``` shell
sudo apt-get install libncurses5-dev
```

Now run the first make command for configuring your kernel:
``` shell
sudo make menuconfig
```

This will start up a graphical interface. The realtime settings are under *Processor type and features*. Select the Preemption Model and choose **Fully Preemptible Kernel (RT) like in the screenshot below:
![](gfx/Preemption_Model_menuconfig.png)

If this option is not available the patch was not successful and something went completly wrong. Try to patch the downloaded kernel once again.


