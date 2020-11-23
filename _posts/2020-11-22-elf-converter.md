---
layout: article
show_date: false
---

# ELF Converter

In general, a GNU compiler toolchain compiles our C/C++ source code into an executable file that can run in Linux system, which is called Executable Linux File (ELF). With a GNU cross compiler, we can compile our source code into program targetting the expected embeded system. However, some embeded systems are designed for low-power scenes. Their computation power is too limited to run a Linux system. Such an embedded system is called bare machine / bare metal. To make the ELF program runnable on bare metal systems, I developed ELF converter to convert ELF files into firmware for M2, which is a bare machine. ELF converter can analyze an ELF file and extract the necessary binary segments to assemble a firmware. ELF converter can be modified to support other SoCs easily. [Link to the project repo](https://github.com/daizhirui/Camel_convert)
