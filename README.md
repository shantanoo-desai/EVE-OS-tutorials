# EVE-OS-tutorials

Getting Acquainted with EVE-OS, Eden and Adam Ecosystem for Edge Computing

> Project EVE is building EVE-OS, a universal, open Linux-based operating
system for distributed edge computing. EVE-OS aims to do for the distributed 
edge what Android did for mobile by creating an open foundation that simplifies 
development, orchestration and security of edge computing nodes deployed 
on-prem and in the field.

## Purpose

This repository tries to bring people quickly on-board the EVE-OS ecosystem through
concise steps within each tutorials. These tutorials can be used as __Quickstart Guides__
to understand what generally happens when using EVE (emulated/real) devices.

### Requirements

* Host Machine: x86_64 / ARM64 machine with a GNU/Linux Distribution / MacOS (Windows Machine hasn't been tested!)
* Virtualization Enabled on the Host Machine (for using `docker`)
* arm64 / amd64 Single Board Computers

## Content

1. _Getting Started with EVE-OS in an Emulated Environment using QEMU_

    1.1 [Setup Host Machine for EVE-OS & Deploy `nginx` App on QEMU](https://github.com/shantanoo-desai/EVE-OS-tutorials/blob/master/00-Eve-Eden-Local-QEMU.md)

    1.2 [Observe CPU / Memory Usage of emulated EVE device using InfluxDBv2 & Telegraf](https://github.com/shantanoo-desai/EVE-OS-tutorials/blob/master/01-Eve-Eden-QEMU-telegraf.md)

2. _Deploying an Actual EVE device using a single-board computer_

    2.1 [Setup a Raspberry Pi 4 as an EVE Device & Deploy `nginx` App on it](https://github.com/shantanoo-desai/EVE-OS-tutorials/blob/master/02-Eve-Eden-RPi4-nginx.md)

## Resources for Project EVE

- [LF-Edge Landing Page for EVE](https://www.lfedge.org/projects/eve/)
- [GitHub Docs for Eden](https://github.com/lf-edge/eden/tree/master/docs)
- [Confluence Wiki Page for EVE-OS](https://wiki.lfedge.org/display/EVE/EVE)