# Installation der PCAN USB Treiber unter Linux mit PREEMPT_RT Patch

```shell
cd ~
wget http://www.peak-system.com/fileadmin/media/linux/files/peak-linux-driver-7.13.tar.gz
tar xf peak-linux-driver-7.13.tar.gz
cd peak-linux-driver-7.13/
make clean KERNEL_LOCATION=/usr/src/linux
make KERNEL_LOCATION=/usr/src/linux PCI=NO_PCI_SUPPORT
sudo make KERNEL_LOCATION=/usr/src/linux PCI=NO_PCI_SUPPORT install
```
