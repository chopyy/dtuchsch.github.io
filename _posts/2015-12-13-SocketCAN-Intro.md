---
layout: post
title:  "Introduction to SocketCAN"
date:   2015-12-13 22:46:00
categories: Linux, CAN
---

> The best way to get started with SocketCAN under Linux is the [kernel documentation](https://www.kernel.org/doc/Documentation/networking/can.txt). In the following guide I will show you on how to configure and set up a CAN device under Linux. I will also give a short introduction on how to send and receive CAN frames via SocketCAN programmatically.

# Available CAN interfaces
You can list all your CAN interfaces as well as all the other network interfaces by typing `ifconfig -a`. This should list all the interfaces available.

# Configure the bitrate
First you will need to configure the bitrate the CAN device will send and receive messages via CAN. Open up a terminal and type `ip link set up can<X> type can bitrate <bitrate>`, where `<X>` stands for its interface number (e.g. can0, can1, can42) and `<bitrate>` specifies the desired bitrate. The following snippet shows an example of configuring the CAN interface can0 and set its bitrate to 125 kBit/s.

```bash
ip link set up can0 type can bitrate 125000
```

# Bring up the CAN interface
After configuring the bitrate, we are now able to bring up the CAN interface by typing `ifconfig can<X> up` into a terminal. If you want to disable a CAN interface just type `ifconfig can<X> down`.

# Send and receive CAN frames
You may easily test your CAN interface by using *can-utils* that is available at [GitHub](https://github.com/linux-can/can-utils). It's a collection of user-space applications to send and receive via SocketCAN. You can clone this repository on your local machine by typing `clone https://github.com/linux-can/can-utils`.

For sending messages via CAN type `./cansend <device> <can_id>#<byte(s)>` as in the following snippet is shown:

```bash
cd can-utils
./cansend can0 055#ac1d
```

It's also possible to receive messages by typing `./candump <device>` as shown in the following example:

```bash
./candump can0
```

This does a blocking read on the socket (synchronized) and prompts the message in the terminal as soon as a CAN frame is received.

*Note: The can-utils repository has some additional packages to offer you can play with and the source code is a first example to programmatically send and receive via SocketCAN.*

# Programming example
It's also possible to access the CAN device programmatically to send and receive CAN frames via SocketCAN.
