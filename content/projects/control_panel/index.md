---
title: "Project: Time Control Panel"
date: "2024-10-07"
lastmod: "2024-12-23"
draft: true
description: "A TinyGo project implementing a multi-functional control panel with knobs and displays"
cover:
  image: "cover.webp"
  relative: true
status: "active"
platform:
  - "Raspberry Pi Pico (2x)"
  - "TM1637 LED Displays"
  - "DFPlayer Mini"
technologies:
  - "TinyGo"
  - "Go"
  - "UART"
  - "Flash Memory"
links:
  story: "/blog/control_panel"
---

## Overview

A hardware project that creates a multi-functional time control panel using modern microcontrollers and Go programming language. The system uses two Raspberry Pi Picos to manage different time displays, complete with sound effects and persistent memory.

## Features

- Three time displays (Target, Current, Previous)
- Real-time clock functionality
- Rotary encoder input for time setting
- Sound effects via DFPlayer Mini
- Persistent memory for time settings
- Inter-controller communication via UART
- Time travel simulation with visual and audio feedback

## Technical Stack

### Hardware

- 2× Raspberry Pi Pico
- 7-segment LED displays with TM1637 chip
- Rotary encoders for time setting
- DFPlayer Mini for audio playback
- SD card for sound storage

### Software

- TinyGo compiler for microcontrollers
- Go standard library features:
  - Goroutines for concurrent operations
  - Channels for event handling
  - Time and date manipulation
- UART communication protocol
- Flash memory operations

## Implementation Details

### Multi-Controller Architecture

- Left Pico: Handles target time setting and display
- Right Pico: Manages current time and previous time
- UART communication for time synchronization

### Time Management

- RFC3339 time format for inter-controller communication
- Real-time clock implementation
- Persistent storage in flash memory
- Gray code implementation for rotary encoders

### Sound Integration

- MP3 playback via DFPlayer Mini
- Sound effects triggered by time travel events
- UART commands for audio control

## Current Status

- Core functionality working
- Time setting and display operational
- Sound effects implemented
- Flash memory persistence working

## Future Plans

- [ ] Add GPS integration for actual speed detection
- [ ] Implement speed-based trigger for time travel
- [ ] Create DIY kit version
- [ ] Add Pomodoro timer mode
- [ ] Develop chess clock functionality
- [ ] Improve sound effect timing

## Updates

### 2024-12-23: Documentation Update

- Converted project documentation to new format
- Added detailed technical specifications
- Updated future plans

[Read the full story about this project](/blog/control_panel)
