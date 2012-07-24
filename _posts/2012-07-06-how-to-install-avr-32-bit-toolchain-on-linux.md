---
layout: post
title: "How to install AVR 32-bit Toolchain on Linux"
description: "Step by step instructions to install AVR 32-bit Toolchain"
author: "Kim Blomqvist"
author-email:

tags:
- "avr toolchain"

categories:
- article

level: beginner

tweet-text: "How to install AVR 32-bit Toolchain on Linux #aery32devzone"

summary: "There are a quite amount of confusion how to install AVR Toolchain on Linux and how to use it standalone without IDE. To make this process easier I decided to describe the installation process step by step"
---

There are a quite amount of confusion how to install AVR Toolchain on Linux and how to use it standalone without IDE (integrated development environment). This short article describes, step by step from scratch, how to install the toolchain and compile the *.c source* file to *ihex binary*.

## Installation

For AVR32, there are two choices to install AVR Toolchain. These are the installation from the binaries that can be downloaded from the Atmel's website, or the installation from sources with [AVR32 Toolchain Builder](https://github.com/jsnyder/avr32-toolchain). This article defines the installation process with the precompiled binaries from Atmel.

### Step 1. Download the files

Go to the Atmel's website for [Atmel AVR Toolchain 3.4.0 for Linux](http://www.atmel.com/tools/ATMELAVRTOOLCHAINFORLINUX.aspx). You have to download two files, which are

- Atmel AVR 32-bit Toolchain 3.4.0 - Linux 32-bit
- Atmel AVR 8-bit and 32-bit Toolchain 3.2.3 - Header Files

If you have a 64-bit Linux, you may like to download the 64-bit version of the Toolchain. However, the 32-bit Toolchain works as well with the 64-bit Linux.

### Step 2. Unzip the downloads to your installation directory

I assume you downloaded the files under the `~/Download` directory. Change to that directory and untar/unzip the files there.

    $ cd ~/Download
    $ tar -xvzf avr32-gnu-toolchain-3.4.0.332-linux.any.x86.tar.gz
    $ unzip avr-headers.zip

The installation is just as easy as moving the files and updating the PATH environment variable. First let's move the files to the directory we like to store them (the installation directory). I like to keep these files under `~/avr32-tools`.

    $ mv avr32-gnu-toolchain-linux_x86 $HOME/avr32-tools
    $ mv avr-headers/avr32 $HOME/avr32-tools/avr32/include

### Step 3. Set PATH

Now update the PATH

    $ export PATH=$PATH:$HOME/avr32-tools/bin

and test that the avr32-gcc is accessible by checking its version

    $ avr32-gcc --version
    avr32-gcc (AVR_32_bit_GNU_Toolchain_3.4.0_332) 4.4.3
    Copyright (C) 2010 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

To complete the installation, you most probably want to export the new PATH variable from your `.bash_profile`, `.zshrc`, etc. So append the file accordingly with the export statement given above.

## Compile with the avr32-gcc

Write a small program that sets PA00 pin high and save it to `main.c`.

<pre class="prettyprint lang-c">
#include &lt;avr32/io.h&gt;

int main(void)
{
	AVR32_GPIO.port[0].oders = 1;
	AVR32_GPIO.port[0].ovrs = 1;

	for (;;) {
	}

	return 0;
}
</pre>

To compile this source file, call avr32-gcc to create the `.elf` binary file and after then use avr32-objcopy to create a `.hex` file from that.

    $ avr32-gcc -mpart=uc3a1128 -std=gnu99 -O2 -Wall main.c -o main.elf
    $ avr32-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature main.elf main.hex

Be sure to change the *mpart* according the part you are using. In addition, we don't want *.eeprom*, *.fuse* and *.lock* sections to our hex, thus `-R .eeprom -R .fuse -R .lock -R .signature`, where -R states for remove.

To see how much space the compiled program takes, use avr32-size

    $ avr32-size -B main.hex
    text    data     bss     dec     hex filename
       0    6022       0    6022    1786 main.hex

I hope this helped you to get started on Linux. If you have any queries, please leave a comment. &#10065;