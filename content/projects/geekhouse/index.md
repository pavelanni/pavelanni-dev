---
title: "Project: Geekhouse"
date: "2024-12-10"
lastmod: "2024-12-10"
draft: false
description: "A smart dollhouse that understands natural language commands in multiple languages"
cover:
  image: "keyestudio_house_esp32.png"
  relative: true
status: "active"
platform:
  - "Raspberry Pi Pico W"
  - "Keyestudio Smart House Kit"
technologies:
  - "MicroPython"
  - "Python"
  - "REST API"
  - "HATEOAS"
  - "Anthropic Claude"
links:
  github: "https://github.com/pavelanni/geekhouse"
  story: "/blog/geekhouse_story"
---

## Overview

Geekhouse is a smart dollhouse project that combines IoT devices with Large Language Models to create a natural language interface for controlling physical devices. Users can control LEDs, motors, and sensors using commands in any language, which are automatically translated into REST API calls by an LLM.

## Features

- Natural language control of physical devices
- Multilingual support (demonstrated with English, Russian, Spanish)
- HATEOAS-compliant REST API for device discovery
- Real-time sensor monitoring
- Support for various actuators:
  - LEDs (multiple colors and locations)
  - DC motors
  - Servo motors
- Human-friendly responses from sensors

## Technical Stack

### Hardware

- Raspberry Pi Pico W (main controller)
- Keyestudio Smart House Kit components:
  - Light sensors
  - Rain sensors
  - LED arrays
  - DC motors
  - Servo motors

### Software

- MicroPython with Microdot framework for REST API
- Python client application
- Anthropic Claude (Haiku-3.5) for natural language processing
- HATEOAS-based API architecture

## Implementation Details

### REST API Design

The API follows HATEOAS principles, providing self-documenting endpoints:

```json
{
  "_links": {
    "self": { "href": "/leds" },
    "filter_by_location": {
      "href": "/leds/filter?location={location}",
      "templated": true
    }
  },
  "data": {
    "1": {
      "color": "yellow",
      "location": "roof",
      "state": 0,
      "_links": {
        "on": { "href": "/leds/1/on" },
        "off": { "href": "/leds/1/off" }
      }
    }
  }
}
```

### LLM Integration

- System prompt includes complete API structure for context
- Uses Haiku-3.5 for optimal speed/cost balance
- Two-stage processing:
  1. Command translation to API calls
  1. Response formatting to natural language

## Current Status

- Core functionality working and demonstrated
- Successfully handles multilingual commands
- Basic sensor monitoring implemented
- Public demo completed at Atlanta Python Meetup

## Future Plans

- [ ] Add LCD display for local status display
- [ ] Integrate additional sensors from 42-in-1 Keyestudio kit
- [ ] Experiment with local LLM deployment for faster response times
- [ ] Add voice input support
- [ ] Implement complex automation scenarios
- [ ] Create a web interface for monitoring

## Updates

### 2024-12-30: LCD implementation; curl to requests

- Added the LCD functionality
- Changed the LLM prompt to generate `requests` calls instead of `curl` commands
- Changed `dialogue.py` accordingly to use `evel()` on the returned function instead of parsing the `curl` command
- Read about it in the blog [curl vs. requests](/blog/curl_vs_requests)

### 2024-12-23: Initial Release

- Completed basic implementation
- Successfully demonstrated multilingual support
- Published project documentation and source code

[Read the full story about this project](/blog/geekhouse_story)
