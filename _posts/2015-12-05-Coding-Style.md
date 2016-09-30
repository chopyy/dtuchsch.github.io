---
layout: post
title:  "File formatting for C and C++"
date:   2015-12-05 19:37:00
categories: cpp
---

> If you're not into coding philosophy, you should stop reading this blog entry immediately :)

I'm very pedantic when it comes to source code style. In my opinion source code files shall have a clean, consistent layout and they shall be easy to read. Therefore, in the past I was looking for a source code comment template that suits my needs. Every single time I thought I had found my coding styleguide, over time I found out the styleguide wasn't complete or I wasn't that consistent. This is life. Every human changes its preferences. And so does my own coding style change over time - I hope I'm not the only one with this...

Even if it is painful - I had to stay strong and commit myself to one well defined source code comment styleguide. The code style is a mix of the following references and my own preferences. Feel free to use and modify it for your own projects.

# Source Files
One translation unit has a defined comment set to group different code elements such as includes, defines, types, static variables and functions. This gives all source files a well defined structure.

## Preferred editor settings

I prefer the  following settings for all of my source code files:

* Tab width: 4 (four spaces)
* Insert spaces for tabs
* Max. line length: 80

## Filecomment
The file comments are at top of each source file. It contains some meta information about the file. For example, the filename, the author's name and a short description how you can use it and why it exists.

```c++
/**
 * @file      my_app.cpp
 * @author    dtuchscherer <daniel.tuchscherer@hs-heilbronn.de>
 * @brief     Brief introduction what this file does.
 * @details   Detailed description how you may use and why this file exists.
 *            Usage: $ ./my_app <arg1>
 * @edit      20.09.2016
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
class MyClass
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

/////////////////////////////////////////////////////////////////////////////////
void hey_foo() noexcept
{

}
 
/////////////////////////////////////////////////////////////////////////////////
int main(int argc, char** argv)
{
    return EXIT_SUCCESS;
}
```

All function bodies are separated by C++ single line comments `//` for better readability.

# Header Files
Basically, the header files are the same compared to source files. Because there is no execution code (except templates) a header file doesn't contain the section `FUNCTION DEFINITIONS`.

# Download
You can download my current code template for Eclipse based on this article [here]({{ site.url }}/assets/codetemplates.xml).
