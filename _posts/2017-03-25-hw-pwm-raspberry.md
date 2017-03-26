---
layout: post
title:  "Hardware Pulse-Width-Modulation (PWM) with the Raspberry PI"
date:   2017-03-25 13:49:00
categories: embedded rpi c++
---

> I want to generate a PWM of 40 kHz with the Raspberry PI for controlling an ultrasonic transceiver. A PWM in software will reach its limits very fast and isn't suited for this type of frequency. Especially, if you want real-time behavior. You may get better results if you're using a PREEMPT_RT patch for the Embedded Linux. But for a PWM frequency greater than 10 kHz even this will not work properly under high load conditions, because the performance of the PREEMPT_RT patch is reached. And prove me wrong: Even tweaking the PREEMPT_RT patch to the fullest, you won't get worst-case-latencies smaller than 50us under high load conditions. Therefore, we need a hardware PWM, which works independent of the Raspberry PI's Embedded Linux. I found a way using the hardware PWM of the Raspberry utilizing `wiringPi`, which I will show in this guide.

## Pre

Before you can get started, you must download, compile and install the [wiringPi](http://wiringpi.com/download-and-install/) library onto your Raspberry PI to drive the GPIOs:

```
$ git clone git://git.drogon.net/wiringPi
$ cd ~/wiringPi
$ ./build
```

I'm using a Raspberry PI 2 with PREEMPT_RT patched Linux kernel. Please note that only wiringPi Pin 1 is able to generate the PWM output [^RPI-GPIO-Map].

## Calculate the clock divider and the necessary range to get a certain PWM frequency

The base clock frequency of the Raspberry PI is 19.2 MHz.

\\[ f_{base} = 19.2 MHz \\]

Let's say you want a PWM frequency of \\(f = 40 kHz\\), which gives a time period of \\(T = 25 \mu s\\). Given the base clock frequency and the PWM frequency you are able to calculate the clock divider and the ticks for the timer:

\\[ \frac{f_{base}}{f_{PWM}} = \frac{19.2 MHz}{40 kHz} = 480 \\]

The factor of 480 will now be separated on the clock divider and the range value.

If you want a high resolution, you may choose the range value greater than the clock divider. For a 40 kHz PWM I choose a clock divider of 2 and a range of 240: \\(240 \cdot 2 = 480\\)

If you divide the PWM period by the range value, you will get the time passed every timer step:

\\[ \frac{T}{240} = \frac{25 \mu s}{240} = 0,1042 \mu s \\]

## Implementation in Modern C++

I'll use C++11 for programming. The C++11 features `constexpr` and `static_assert()` come in handy to do some compile-time calculations and checks to verify the correct settings.

Create a file named `pwm.cpp` and define some `constexpr` variables based on our desired PWM frequency and define the clock divider and the steps value based on the calculations we made:

```c++
// Do not edit: This is the base clock frequency of the RPI. It's 19.2 MHz.
static constexpr double f_base = 19200000.0;
// Specifiy your PWM frequency
static constexpr double f = 40000.0;
// f = 40 kHz -> T = 25 us
// 19.2 MHz / 2 / 240 = 40 kHz
// Specify the clock divider for the base clock frequency.
static constexpr unsigned int clock_div = 2U;
// Specify the steps for the PWM modul to count up, until a new period is
// started. This is an additional divider. Variable "steps" should be greater
// than the clock_div value to get more precision for the duty cycle.
static constexpr unsigned int steps = 240U;
// Calculate the period from the PWM frequency for later use
static constexpr double T = 1 / f;
```

Then, we check if the clock divider and the steps have the correct value to produce the desired PWM frequency.

```c++
// +- Set a tolerance for asserting the calculated frequency
static constexpr double tolerance = 10.0;
// Calculate the frequency based on the dividers and the base clock frequency
// to check if the clock divider and the steps are initialized with the correct // values.
static constexpr double f_calc =
    f_base / static_cast< double >(clock_div) / static_cast< double >(steps);
static constexpr double scale_calc = (T * 1e6) / static_cast< double >(steps);
static_assert((f_calc >= f - tolerance) && (f_calc <= f + tolerance),
              "Check frequency settings. Either clock divider or steps value does not meet the requirements.");
```

 `static_assert()` will throw an error at compile-time if the frequency calculated does not match the desired PWM frequency, preventing the user from run-time errors.

In the following step we define a function `init_pwm()` to initialize the PWM:

```c++
void init_pwm() noexcept
{
    // Note that only wiringPi pin 1 (BCM_GPIO 18) supports PWM output
    pinMode(1, PWM_OUTPUT);
    pwmSetMode(PWM_MODE_MS);
    pwmSetClock(clock_div);
    pwmSetRange(steps); // 25us / 240step = 0,1042 us / step
    // Set the duty cycle
    pwmWrite(1, 100); // 0,1042 us / step * 100 step = 10,4 us
}
```

Calling `pinMode(1, PWM_OUTPUT)` will set the GPIO Pin 1 as PWM output.

To set the duty cycle of the PWM, `pwmWrite()` function is used. To set the clock divider and the range respectively, call `pwmSetClock(clock_div)` and `pwmSetRange(steps)`. With `pwmWrite()` you vary the duty cycle of the PWM.

In the last step we create the `main()` function and initialize both, the wiringPi and the PWM.

```c++
int main() noexcept
{
    const auto init = wiringPiSetup();
    init_pwm();

    if (init == -1)
    {
        exit(-1);
    }

    for (;;)
    {

    }

    return EXIT_SUCCESS;
}
```

## Compile and Run

Create a shell script `compile.sh` and paste in the following snippet:

```
#!/bin/sh
g++ pwm.cpp -std=c++14 -O2 -fno-exceptions -fno-rtti -Wall -Wextra -Wpedantic -lwiringPi -lrt -o pwm
```

The compiler option `-std=c++14` is necessary to activate C++11/C++14 support.

Compile it by typing `$ sh compile.sh` in a terminal. To run the program type `$ sudo ./pwm`. If you connect an oscilloscope channel to wiringPi pin 1 after starting the application, it will produce a PWM as shown in the following diagram.

![]({{ site.url }}/assets/pwm-40khz-rpi.png)

[^RPI-GPIO-Map]: [Raspberry Pi GPIO How-To](http://raspberrypiguide.de/howtos/raspberry-pi-gpio-how-to/)
