---
title: "Project: SSH Login Monitor"
date: "2023-04-27"
lastmod: "2024-12-23"
draft: false
description: "A Go program to monitor SSH logins and identify users by their key fingerprints"
cover:
  image: "cover.webp"
  relative: true
status: "completed"
platform:
  - "Linux"
  - "Red Hat Enterprise Linux"
technologies:
  - "Go"
  - "Regular Expressions"
  - "SSH"
  - "ChatGPT"
links:
  github: "https://github.com/pavelanni/ssh-login-monitor"
  story: "/blog/ssh_login_monitor"
---

## Overview

A Go program that monitors SSH login events in system logs, matches login/logout pairs, and identifies users by their SSH key fingerprints. The project demonstrates practical use of Go for system administration tasks and showcases how AI can assist in development.

## Features

- Parses `/var/log/secure` for SSH login events
- Matches login and logout events by port numbers
- Identifies users via SSH key fingerprints
- Calculates session durations
- Supports CSV-based user database
- Command-line argument support
- Comprehensive test coverage

## Technical Stack

### Platform Requirements
- Linux system with SSH server
- Access to system logs (`/var/log/secure`)
- Go runtime environment

### Software Components
- Go standard library
- `github.com/spf13/pflag` for command-line parsing
- Regular expressions for log parsing
- CSV handling for user database

## Implementation Details

### Log Parsing
- Regular expressions to extract:
  - Timestamps
  - IP addresses
  - Port numbers
  - SSH key fingerprints
  - Login/logout events

### User Identification
- CSV database format: `username,fingerprint`
- Fingerprint matching for user identification
- Support for multiple users with different keys

### Session Tracking
- Port-based login/logout matching
- Duration calculation
- Source IP tracking

## Current Status

- Core functionality complete
- Command-line interface implemented
- Test suite in place
- Two versions available:
  - Original Go implementation
  - ChatGPT-generated version

## Future Plans

- [ ] Add real-time monitoring mode
- [ ] Implement log rotation handling
- [ ] Add support for different log formats
- [ ] Create configuration file support
- [ ] Add output formatting options
- [ ] Implement statistics collection

## Updates

### 2024-12-23: Documentation Update
- Converted to new documentation format
- Added detailed technical specifications
- Reorganized project structure

[Read the full story about this project](/blog/ssh_login_monitor)
