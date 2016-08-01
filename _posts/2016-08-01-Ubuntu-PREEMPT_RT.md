---
layout: post
title:  "PREEMPT_RT patch for Ubuntu Linux"
date:   2015-06-30 22:56:07
categories: real-time
---

> In this guide i will show you how to patch the Linux kernel. As an example I'm going to use kernel version 3.18.29.

# Configuration
Open up a terminal, go to your home directory and declare some variables we use during kernel configuration.

```shell
KERNEL=linux-3.18.29
RT=patch-3.18.29-rt30
KERNEL_SRC=https://www.kernel.org/pub/linux/kernel/v3.x/${KERNEL}.tar.xz
RT_PATCH_SRC=https://www.kernel.org/pub/linux/kernel/projects/rt/3.18/${RT}.patch.xz
```

The `_SRC` variables are the paths to download the kernel and the PREEMPT_RT patch. 
In the next step download the kernel, extract the files and rename the directory to `-rt`.

```shell
wget ${KERNEL_SRC}
tar -Jxvf ${KERNEL}.tar.xz
mv ${KERNEL} ${KERNEL}-rt
rm ${KERNEL}.tar.xz
cd ${KERNEL}-rt
```

Now, create a directory named `patches`, download the PREEMPT_RT patch, extract it and apply the patch.

```shell
mkdir patches
cd patches
wget ${RT_PATCH_SRC}
xz -dk ${RT}.patch.xz
touch series
echo "${RT}.patch" > series
cd ..
quilt push -a
```

Next, we need a kernel .config file that has the correct settings already. Copy the kernel configuration file from the Ubuntu Linux that is
installed.

```shell
cp /boot/config-$(uname -r) .config
```

Start the configuration menu:

```shell
make menuconfig
```

Choose `Fully Preemptible Kernel (RT)` in `Kernel Features`.

# Kernel Compilation

Compile the kernel and build Debian packages:

```shell
cd ${KERNEL}-rt
make-kpkg clean
sudo time make-kpkg --initrd --revision=0 kernel_image kernel_headers
```

# Installation
Install the Debian packages:

```shell
sudo dpkg -i linux-image* linux-headers*
```
