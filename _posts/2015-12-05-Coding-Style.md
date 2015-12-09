---
layout: post
title:  "Source Code Comment Styleguide for C and C++"
date:   2015-12-05 19:37:00
categories: cpp
---

> If you're not into coding philosophy, you should stop reading this blog entry immediately :)

I'm very pedantic when it comes to source code style. In my opinion source code files shall have a clean, consistent layout and they shall be easy to read. Therefore, in the past I was looking for a source code comment template that suits my needs. Every single time I thought I had found my coding styleguide, over time I found out the styleguide wasn't complete or I wasn't that consistent. This is life. Every human changes its preferences. And so does my own coding style change over time - I hope I'm not the only one with this...

Even if it is very painful - I had to stay strong and commit myself to one well defined source code comment styleguide. The code style is a mix of the following references and my own preferences. Feel free to use and modify it for your own projects.

# Source Files
One translation unit has a defined comment set to separate different code elements like includes, defines, types, static variables and functions. This makes the source file more readable.

## Filecomment
The file comments are in the head of each source file. It contains information about the file name, its author.

```c++
/**
 * @file      rt_can.cpp
 * @author 	  dtuchscherer <daniel.tuchscherer@hs-heilbronn.de>
 * @brief     Example of send and receive via SocketCAN
 * @details   This demo shows the communication via Linux SocketCAN.
 *            It contains two POSIX threads talking to each other via SocketCAN.
 *            A transmission thread sends CAN frames containing a timestamp
 *            with the time point of transmission via a CAN interface.
 *            The other thread receives those CAN frames with a loop with a
 *            blocking read on another CAN interface.
 * 			      Usage: $ ./rt_can <can_dev> <can_dev> [cycles]
 * @version   1.2
 */
```

In addition you could place a copyright notice here by `@copyright Copyright (c) 2015, Daniel Tuchscherer.` and add a license, too.

## File structure
On the file comment there follows the structure to group the code elements: 

```c++
/*******************************************************************************
 * MODULES USED
 *******************************************************************************/
#include <array>
#include "Platform_Types.h"

/*******************************************************************************
 * DEFINITIONS AND MACROS
 *******************************************************************************/
constexpr int RT_PRIORITY = 97;

/*******************************************************************************
 * TYPEDEFS, ENUMERATIONS, CLASSES
 *******************************************************************************/
class Block
{

};

/*******************************************************************************
 * PROTOTYPES OF LOCAL FUNCTIONS
 *******************************************************************************/

/*******************************************************************************
 * EXPORTED VARIABLES
 *******************************************************************************/

/*******************************************************************************
 * GLOBAL MODULE VARIABLES
 *******************************************************************************/
static uint8 Module_var = 0U;

/*******************************************************************************
 * EXPORTED FUNCTIONS
 *******************************************************************************/

/*******************************************************************************
 * FUNCTION DEFINITIONS
 *******************************************************************************/
int main(int argc, char** argv)
{

}
```

# Header Files
Basically, the header files are the same compared to source files. Because there is no execution code a header file doesn't contain the section `FUNCTION DEFINITIONS`.

# Download
You can download my current code template for Eclipse that is based on this article [here]({{ site.url }}/assets/codetemplates.xml).
