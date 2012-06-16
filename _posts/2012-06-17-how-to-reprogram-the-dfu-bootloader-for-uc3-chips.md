---
layout: post
title: "How to reprogram the DFU Bootloader for UC3 chips"
description: ""
author: "Kim Blomqvist"
author-email:

tags:
- bootloader
- dfu
- jtag

categories:
- article

level: guru

tweet-text: "How to reprogram the DFU Bootloader for UC3 chips #aery32devzone"

summary: "Sometimes it happens and you have accidently erased the DFU Bootloader from your board; or either you want to change the Bootloader to the previous version or update to the newer one. This article describes how to do that"
---

<img class="pull-right" itemprop="image" src="http://devzone.aery32.com/images/reprogramming_bootloader_of_aery32.png" alt="AVR Dragon connected to Aery32">

Sometimes it happens and you have accidently erased the DFU Bootloader from your board; or either you want to change the Bootloader to the previous version or update to the newer one. [Atmel's document number 7745](http://www.atmel.com/Images/doc7745.pdf) describes the UC3 Bootloader and how to reprogram it. The document mentions the `avr32program` command line tool that can be used to do the job. However, it is not obvious where to get that. Neither it has been delivered with the standalone AVR Toolchain nor with the latest Atmel Studio 6. Though, Studio 6 includes a new tool called `atprogram`, it is not clear if it can be used for reprogramming the Bootloader. If someone knows better, please leave a comment.

## Reprogramming the Bootloader is not the simplest task

You will need these:

- [Ubuntu Hardy Heron](http://releases.ubuntu.com/8.04/) &ndash; as fas as I know the `avr32program` works only on Ubuntu and only in this version
- [AVR UC3 Software Framework 1.7.0](http://www.atmel.com/Images/AVR-UC3-SoftwareFramework-1.7.0.zip) &ndash; this is where the Bootloader binaries can be found
- JTAG programmer &ndash; AVR Dragon has been used here

The easiest way to install Ubuntu Hardy Heron is most probably using virtualization software, for example Oracle's [VirtualBox](https://www.virtualbox.org/).

When you have logged in Hardy Heron change to the root

	sudo su

and add `deb http://distribute.atmel.no/tools/avr32/release/ubuntu/ hardy main` to the sources.list. You can edit the sources.list file through nano for example

	nano /etc/apt/sources.list

After then install `avr32program` and `avr32-gnu-toolchain`

	apt-get update
	apt-get install avr32program avr32-gnu-toolchain

Now download the UC3 Software Framework 1.7.0

	mkdir Downloads
	cd Downloads
	wget http://www.atmel.com/Images/AVR-UC3-SoftwareFramework-1.7.0.zip
	unzip AVR-UC3-SoftwareFramework-1.7.0.zip

The Bootloader binaries (for UC3A) can be found from the `1.7.0-AT32UC3/SERVICES/USB/CLASS/DFU/EXAMPLES/ISP/AT32UC3A/Releases/` directory. For Aery32 Development board I chose 1.0.3. Its newer version than what comes wiht the board (UC3A1128 chips are shipped with version 1.0.2).

Connect the AVR Dragon to the board via JTAG interface. If you are on native Linux you can run the shell script that performs all the steps for you

	cd AT32UC3A-ISP-1.0.3/
	chmod u+x program_at32uc3a-isp-1.0.3.sh
	./program_at32uc3a-isp-1.0.3.sh

Otherwise, if you are on VirtualBox you have to do all the steps manually, because VirtualBox keeps detaching AVR Dragon from the VM after every task that has been sent for it. You can reattach the Dragon to the VM by right clicking the USB cable icon from the lower right hand side bar. For steps check inside the `program_at32uc3a-isp-1.0.3.sh`

	cat program_at32uc3a-isp-1.0.3.sh

<span class="label label-important">Important!</span> After reprogramming the Bootloader of the Aery32 Development board, you have to reconfigure the fuses for DFU switch. In Windows this can be done by calling `make batchisp-update-userdata`.

&#10065;