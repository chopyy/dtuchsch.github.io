---
layout: post
title:  "Introduction to SocketCAN"
date:   2015-12-13 22:46:00
categories: Linux CAN SocketCAN
---

> This document gives a brief introduction on how to use SocketCAN under Linux. SocketCAN is an utility for Linux to use Controller Area Network (CAN) fieldbus.

> Take a look at [kernel documentation](https://www.kernel.org/doc/Documentation/networking/can.txt) for a brief introduction to SocketCAN. In the following guide I will give a short introduction on how to configure, set up and use a CAN device under Ubuntu Linux using SocketCAN.

# Check for CAN interfaces

Open up a terminal. List all the available interfaces: `$ ifconfig -a`. This command will list all CAN devices available - even if some of them are not active yet. If you've plugged in your CAN USB or CAN PCI card of choice you should see something similar to this:

```
can0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          UP RUNNING NOARP  MTU:16  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:10
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:16
```

If there are no CAN devices listed, make sure the CAN driver for your CAN hardware device is installed properly. If there is no real CAN hardware available, SocketCAN also provides a virtual interface. For more information about vcan take a look below.

# Virtual CAN

Virtual CAN `vcan` makes it possible to send and receive CAN frames on your local machine without the need of a CAN hardware device. Before you're able to use it, you must add a vcan device:

```
$ sudo modprobe vcan
$ sudo ip link add dev vcan0 type vcan
```

This will add a virtual CAN device vcan0 to your system. To send and receive data, activate it:

```
$ sudo ifconfig vcan0 up
```

Typing `$ ifconfig ` in a terminal should give you a list of all network interfaces with your added vcan0 device.

## Using CAN FD

For standard CAN frames the MTU is set to 16.
To use CAN FD, adjust the maximum transmission unit. For CAN FD the MTU must be set to 72:

```
$ sudo ip link set vcan0 mtu 72
```

# Configure the CAN device's bitrate

> Please note: Configuring the bitrate of a CAN device only applies for real CAN hardware. You may skip this section, if you use virtual CAN.

Set the bitrate of your CAN device to send and receive data. Open up a terminal and type `$ ip link set up can<X> type can bitrate <bitrate>`, where `<X>` stands for the interface number (e.g. can0, can1, ...) and `<bitrate>` specifies the desired bitrate. The following snippet shows an example to configure the CAN interface `can0` and set its bitrate to 125 kBit/s.

```bash
$ ip link set up can0 type can bitrate 125000
```

# Activate & deactivate the CAN interface

After you've set the bitrate, you're now able to bring up the CAN interface: `ifconfig can<X> up`. To deactivate the interface again type `ifconfig can<X> down`.

# Send and receive CAN messages via SocketCAN
By using *can-utils* available at [GitHub](https://github.com/linux-can/can-utils) you can test your CAN device. *can-utils* is a collection of user-space applications to send, receive and monitor data via SocketCAN. You can clone this repository on your local machine: `clone https://github.com/linux-can/can-utils`.

> In many cases can-utils is already part of the kernel and the user-space applications are located under /usr/bin.

For sending messages with SocketCAN type `cansend <device> <can_id>#<byte(s)>` as shown in the following snippet:

```bash
cansend can0 055#ac1d
```

It's also possible to receive messages by typing `candump <device>` as shown in the following example:

```bash
candump can0
```

This does a blocking read on the socket (synchronized) and prompts the message in the terminal as soon as one CAN message is received.

*Note: The can-utils repository has some additional packages to offer you can experiment with. Check out the C source code in this [can-utils GitHub repository](https://github.com/linux-can/can-utils) how to programmatically send and receive data over SocketCAN.

# Register one CAN device on startup of Linux

If you want to set the bitrate and bring up a CAN interface at start up time of Linux automatically, you have to edit `/etc/network/interfaces`:

Open up the file `/etc/network/interfaces` in an editor of your choice and insert the following into `/etc/network/interfaces` at the end:

```
allow-hotplug can0
iface can0 can static
    bitrate 125000
    ifconfig $IFACE down
    ip link set $IFACE type can bitrate 125000
    ifconfig $IFACE up
```

These settings will initialize a CAN device automatically with a given bitrate (e.g. "can0") at boot-up time. Please note: Adjust the bitrate to your needs. As an example the bitrate is set to 125 kBit/s.

# Setup Virtual CAN device on startup of Linux

Open up `/etc/modules` in an editor, add `vcan`. This will load the module at boot time.

As mentioned for a real CAN device, edit `/etc/network/interfaces` and paste in the following:

```
auto vcan0
   iface vcan0 inet manual
   pre-up /sbin/ip link add dev $IFACE type vcan
   pre-up /sbin/ip link set vcan0 mtu 72
   up /sbin/ifconfig $IFACE up
```

To apply these changes and use vcan0 you may either restart the network settings by typing `$ sudo /etc/init.d/networking restart` or reboot Linux.

Interface vcan0 should pop up if you type `$ ifconfig` in a terminal.

# Recap

* Add a virtual CAN device: `$ sudo ip link add dev vcan<X> type vcan`
* Adjust the bitrate settings: `$ sudo ip link set can<X> bitrate <XXXX>`
* Activate a SocketCAN interface: `$ sudo ifconfig can<X> up`
* Shut down a SocketCAN network device: `$ sudo ifconfig can<X> down`
