---
layout: post
title:  "Introduction to SocketCAN"
date:   2015-12-13 22:46:00
categories: Linux, CAN
---

> The best way to get started with SocketCAN under Linux is the [kernel documentation](https://www.kernel.org/doc/Documentation/networking/can.txt). In the following guide I will show you on how to configure and set up a CAN device under Linux. I will also give a short introduction on how to send and receive CAN frames via SocketCAN programmatically.

# Available CAN interfaces
You can list all your network interfaces by typing `ifconfig -a`. This should list all the interfaces available. If there's no CAN device listed, you should fix your kernel or plug in your CAN USB interface.

# Configure the bitrate
First you will need to configure the bitrate the CAN device will send and receive messages via CAN. Open up a terminal and type `ip link set up can<X> type can bitrate <bitrate>`, where `<X>` stands for its interface number (e.g. can0, can1, can42) and `<bitrate>` specifies the desired bitrate. The following snippet shows an example of configuring the CAN interface can0 and set its bitrate to 125 kBit/s.

```bash
ip link set up can0 type can bitrate 125000
```

# Bring up the CAN interface
After configuring the bitrate, we are now able to bring up the CAN interface by typing `ifconfig can<X> up` into a terminal. If you want to disable a CAN interface just type `ifconfig can<X> down`.

# Send and receive CAN frames
You may easily test your CAN interface by using *can-utils* that is available at [GitHub](https://github.com/linux-can/can-utils). It's a collection of user-space applications to send and receive via SocketCAN. You can clone this repository on your local machine by typing `clone https://github.com/linux-can/can-utils`.

> In many cases can-utils is already part of the kernel and the user-space applications are located under /usr/bin.

For sending messages via CAN type `cansend <device> <can_id>#<byte(s)>` as in the following snippet:

```bash
cansend can0 055#ac1d
```

It's also possible to receive messages by typing `candump <device>` as shown in the following example:

```bash
candump can0
```

This does a blocking read on the socket (synchronized) and prompts the message in the terminal as soon as a CAN frame is received.

*Note: The can-utils repository has some additional packages to offer you can play with and the source code is a first example to programmatically send and receive via SocketCAN.*

# Programming example
It's also possible to access the CAN device programmatically to send and receive CAN frames via SocketCAN. For this purpose I created a class named Can. The constructor opens a socket and binds the socket to the CAN interface given as the constructor's parameter.

```c++
class Can
{
public:
    /**
     * @brief Open a CAN socket and bring up the can interface.
     * @param[in] ifrname interface name, e.g. "can0", "vcan0"
     */
    Can(const char* ifrname) noexcept;

    /**
     * @brief Destructor closes the file descriptor of the socket.
     */
    ~Can() noexcept;

    /**
     * @brief Transmits a message on CAN bus.
     * @param[in] can_id CAN identifier to transmit the message.
     * @param[in] data_ref This is an array of bytes that contains
     * the packet and is being transmitted on CAN bus with the given CAN ID.
     * @param[in] len Length in bytes to send.
     * One CAN packet may contain 8 bytes at a maximum.
     * @return the length transmitted.
     * @remarks If you want to send more than 8 bytes within a message
     * you need a transport layer like CanTp / IsoTp.
     */
    sint8 send(const uint16 can_id,
               std::array< uint8, 8U >& data_ref,
               const uint8 len) noexcept;

    /**
     * @brief Receives a CAN message from the socket and
     * writes the data into an array.
     * @param[out] can_id CAN identifier of the message received.
     * @param[out] data_ref Array to store the received packet data to.
     * @return Greater than zero if data was received.
     * This returns -1 if there was an error or timeout.
     */
    sint8 receive(uint16& can_id, std::array< uint8, 8U >& data_ref) noexcept;

private:
    struct ifreq m_ifr; //!< Holds the index of the interface in a struct if
                        //!< the interface exists.
    struct sockaddr_can m_sockaddr; //!< Holds the address family CAN and
                                    //!< the interface index to bind the socket on.
    int m_socket_fd; //!< File descriptor of the socket
                     //!< the CAN data is sent over.
                     //!< This attribute is set after initialization of the object.
    boolean m_init; //!< Boolean flag if the CAN is initialized.
                    //!< This means a socket is opened and the CAN interface exists.
};
```

The destructor closes the socket again. The send is done by a write to the socket file descriptor. Other way around you may receive data with a blocking read by calling `receive()`. So the usage is quite simple. The following snippet shows an example how to use this class. First, we will create an object `can` and give the constructor the CAN interface `"can0"` as a string. In the next step we create an `std::array` of 8 bytes on the stack and send the CAN frame with a call to `can.send()`.

> If you want to send more than eight bytes via CAN you need a transport layer like CanTp aka ISO-TP.


```c++
#include <array>
#include "Can.h"

Can can("can0");

std::array< uint8, 8U > can_data;

can_data[0] = 0xFFU;
can_data[1] = 0x11U;
can_data[2] = 0xACU;

// Send out the data via CAN.
sint8 send_res = can.send(0x12U, can_data, 3U);
```

You can also send messages periodically *in time* by calling this `can.send()` within a loop of a high priority real-time thread using PREEMPT_RT patch.
