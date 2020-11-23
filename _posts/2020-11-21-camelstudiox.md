---
layout: article
title: CamelStudio X
show_date: false
---

# CamelStudio X

CamelStudio X is an IDE designed for developing software running on SoC designed by Camel Microelectronics Inc. 

<img src="/assets/images/camelstudiox/welcome.png" width=800px aligned="center">

## Why It Came to the World

When I was taking the course of SoC as a sophomore, I found there was no IDE for developing the targeted SoC on my MacBook Pro. As a geek, I launched this project and finished it independently. So, CamelStudio X was borned! Now, students can use CamelStudio X for learning how to programming the embedded system in the course on their favorite macOS devices.

## Core Modules

To make CamelStudio X an IDE for hardware development, a bunch of essential modules are required. 

- Cross compiler toolchains for compiling the source code into machine code for the target platform
- A make system for coordinating compiler tools
- ELF converter that can generate the firmware for the targeted embedded system by analyzing the compiler output
- Serial port communication interface that can transmit data between the embeded system and the upper computer

<img src="/assets/images/camelstudiox/camelstudiox.svg" width=800px aligned="center">

## Features and Functionalities

### Simple and User-friendly UI

CamelStudio X is a beautiful IDE. Its UI supports automatic dark theme adaptation. The editor supports syntax highlighting for C, C++, and assembly. The UI is kept as concise as possible to lower the learning threshold.

<img src="/assets/images/camelstudiox/editor.png" width=800px aligned="center">

### Detailed and Well-arranged Project Setting Page

A project of embedded system software can be very complicated. CamelStudio X provides a carefully designed project setting page so that users do not have to write their own Makefile to tell the make system how to compile the source files.

<img src="/assets/images/camelstudiox/serial_and_project.png" width=800px aligned="center">

### Powerful Serial Tool

CamelStudio X provides a serial tool which supports

- uploading program
- switching the chip from the root menu to the targeted program

## Github

CamelStudio X is open-sourced. It is not only an excellent IDE but also a good example of software engineering if you are interested in how an IDE is developed. 

- [Link to the repository](https://github.com/daizhirui/CamelStudioX) (version >= 4.0)
- [Link to the repository](https://github.com/daizhirui/CamelStudioX_Mac) (version <= 3.8)
