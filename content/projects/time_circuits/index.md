---
date: '2024-10-07'
draft: true
title: "Time circuits"
description: "A TinyGo project implementing Time Circuits (for time travel, you know)"
cover:
  image: "cover.webp"

---

You know how it all started? I was browsing Amazon one day (as you do) and came across this set of color 7-segment LED displays.

![Seven-segment LEDs](seven-segment-leds.webp)

Instantly, I thought of the Time Circuits display from _Back to the Future_.

This project combines two of my favorite things — tech tinkering and fun. I used two Raspberry Pi Picos, some 7-segment LED displays, rotary encoders, and (of course) Go! And before you ask — yes, it does something cool, and no, I haven’t hit 88 mph with it just yet.

This isn’t my first attempt at making this thing. I originally started with an Arduino, writing the project in C++. I even got as far as setting the destination time before life happened (probably work). It stayed unfinished for years.

![Version 1.0](version1.webp)
Time Circuits v1.0 (Arduino)

Version 2.0, though? That’s where things got interesting. I picked up TinyGo — a Go compiler for microcontrollers — and decided it was the perfect excuse to learn both TinyGo and Go while having fun. TinyGo works like a charm on Raspberry Pi Pico, so that became my hardware of choice.

![Version 2.0](version2.webp)
Time Curcuits v2.0 (TinyGo and Raspberry Pi Pico)

## The Hardware

Here’s the gear I used:

- Two **Raspberry Pi Picos**
- 4-digit **7-segment LED** displays with the TM1637 chip
- **Rotary encoders** for setting the time (way more satisfying than buttons)
- **DFPlayer Mini** to play sound effects. Because what’s a time-travel toy without some audio drama?

The plan was simple: one Pico would set the “destination time,” and the other would handle the “present time” and “last time departed.” When you press a button imitating the actual time travel, the “destination time” becomes your “present time,” and the fun begins. Sounds cool, right?

## Why TinyGo?

Let’s be honest: when working with microcontrollers, the usual suspects are Arduino, MicroPython, or maybe CircuitPython. But with TinyGo, I got to work in Go — one of my favorite languages. It just made everything more enjoyable (and less tedious).

Here’s why TinyGo rocks:

- **Goroutines**: No more messing around with loops and managing every state yourself. With goroutines, the heavy lifting is done.
- **Channels**: These made it super easy to pass messages around without worrying about blocking the main thread. (Looking at you, Arduino.)


## How it all works

This setup has two brains (aka Raspberry Pi Picos) that talk to each other via UART. Here’s a rough idea of what’s going on:

1. Left Pico: You set the “destination time” using rotary encoders and 7-segment displays.

1. Right Pico: It handles the “present time” and “last time departed” and then updates the times when you hit the button to start the journey.

When you press the “time travel” button, the destination time is sent from the left Pico to the right Pico as an RFC3339 string (fancy, huh?). Then, the present time becomes the new “last time departed” — you know, just in case you want to go back to the exact moment you left.

![Hardware diagram](breadboard.webp)
Hardware connections

## Software Shenanigans

I kept the code simple but flexible. I used goroutines to handle multiple tasks at once — like updating the display while you’re setting the time — and channels to manage button presses and other events without creating a tangled mess of code.

Here’s the basic rundown:

- You turn the rotary encoder to set the year, month, and day for the Destination time.
- The left Pico sends that data over UART to the right Pico.
- When you press the time-travel button, the current time is saved in the “last departed” time, and the time set as the destination becomes your present time.
- The present time starts ticking as your typical wall clock.

## Extra fun stuff: sound and memory

Because time travel needs sound effects (obviously), I hooked up a DFPlayer Mini. It plays MP3s from an SD card, and the Picos send the player commands over UART.

Oh, and the toy remembers your settings. Every second, the times are saved to flash memory. When you power it back on, the Picos pick up right where they left off. Handy, right?

![DFPlayer Mini](dfplayer.webp)
DFPlayer Mini

## Lessons I didn’t expect to learn

This project was a great learning experience but it wasn’t all smooth sailing. Here are some of the surprises I encountered:

1. **Goroutines**: In TinyGo, goroutines will keep going until they hit a blocking operation (like I/O). I had to add tiny sleep calls here and there to make everything work smoothly.

1. **Error handling matters**: Error handling is always important, but it’s really important here. Things will go haywire if an extra character sneaks into your UART transmission. You must detect it and avoid just hanging up the whole time machine.

1. **Rotary encoders are not so simple**: I learned about Gray code (have you heard about it?) to implement the rotary encoders, which saved me from a world of trouble. In short, it’s an alternative binary code where two consecutive numbers differ only by one bit. It turns out to be very useful in error-prone environments like electronic circuits. Read more: https://en.wikipedia.org/wiki/Gray_code.

1. **Debouncing switches**: That’s a whole other world of engineering. When you pressed a button, did it send just one pulse or more? If you’re going down that rabbit hole, here are some excellent reads on switch debouncing: https://www.eejournal.com/article/ultimate-guide-to-switch-debounce-part-1/ and http://www.ganssle.com/debouncing.htm.

## What’s next?

There are so many ways I can build on this project. For starters, I could add a GPS and a speedometer to really nail that 88 mph speed limit and make that trigger the time-travel event instead of a lousy button. Or I could turn this into a Pomodoro timer, an alarm clock, or a chess clock — time-travel toys for all occasions.

Or, who knows? Maybe I’ll create a DIY kit so you can build your own time-travel toy at home.

## Want to try TinyGo?

If you’re curious about TinyGo and want to dip your toes in, here’s how to start:

1. Get a Raspberry Pi Pico Starter Kit — it usually includes sensors, motors, LEDs, and more.
1. Clone the TinyGo repo: https://github.com/tinygo-org/tinygo
1. Follow the steps here: https://tinygo.org/getting-started/

Trust me, it’s a fun way to learn Go, and you’ll get something you can actually hold and show off to your friends.

Please let me know if you need more details about this project. Or if you have ideas about improving it — I’ll be happy to continue working on it!
