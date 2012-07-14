---
layout: post
title: "Batchisp for UC3 chips demystified"
description: "With Batchisp you can program the UC3 chips via USB DFU Bootloader"
author: "Kim Blomqvist"
author-email:

tags:
- dfu
- batchisp

categories:
- article

level: beginner

tweet-text: "Scroll text on HD44780 type display #aery32devzone"

summary: "With Batchisp you can program the UC3 chips via USB DFU Bootloader.  In this case the USB DFU Bootloader shipped within the UC3 chips, allows you to program the chip with your own program by just using the USB interface&ndash;no need for an external programmer hardware"
---

<img class="pull-right" itemprop="image" src="http://devzone.aery32.com/images/dfu_programming.png" alt="DFU programming" width="320" height="240">

<span class="label label-info">Note!</span> Originally posted to [AVR freaks forum](http://www.avrfreaks.net/index.php?name=PNphpBB2&file=viewtopic&t=122767).

With Batchisp you can program the UC3 chips via USB DFU Bootloader. USB Device Firmware Upgrade (DFU) is an official USB device class specification. It specifies a vendor and device independent way of updating the firmware of a USB device. In this case the USB DFU Bootloader shipped within the UC3 chips, allows you to program the chip with your own program by just using the USB interface&ndash;no need for an external programmer hardware. 

## Installing Batchisp 

Batchisp is a command line tool for in-system programming developed by Atmel. It is part of the FLIP and there are installers for Windows and Linux. This post considers using Batchisp on Windows only! 

FLIP requires Java Runtime Enviroment, so be sure to have that installed. It's better to install the newer version from Oracle than installing the JRE as part of the FLIP. 

- [Check if you already have Java JRE](http://java.com/en/download/installed.jsp?detect=jre&try=1)
- [Download and install JRE](http://java.com/en/download/index.jsp)
- [Download and install FLIP](http://www.atmel.com/tools/FLIP.aspx)

Open Windows Command Prompt. Press Win+R (Windows button and R). Type cmd and press Enter. 

Test that the installation works:

	C:\>batchisp -version 
	batchisp version: 1.2.5

If batchisp was not recognized, jump to the troubleshooting section for the fix. 

## Chip programming via DFU 

Batchisp commands are executed in order. So do not mess with the order. The first param to set is the device. I'll use uc3a1128 chip here for the demo. After the device selection, comes the hardware selection. That's usb for the DFU in-system programming: `batchisp -device at32uc3a1128 -hardware usb`

To not repeat myself, I stored this into the DFU variable

	C:\>set DFU=batchisp -device at32uc3a1128 -hardware usb

Now plug the USB cable to your board and program (or upload) your.hex file into the board (you can also use .elf files)

	C:\>%DFU% -operation erase f memory flash blankcheck loadbuffer your.hex program verify

- `erase f`, removes the flash, but not the bootloader section 
- `memory flash`, selects flash as a target memory for the following commands 
- `blankcheck`, checks if the flash is empty 
- `loadbuffer your.hex`, loads your .hex file into the buffer 
- `program`, programs the target memory with the data in the buffer 
- `verify`, tells if there were any errors 

After this you still have to start the board

	C:\>%DFU% -operation start reset 0

You could have done this also along the program command in this way

	C:\>%DFU% -operation erase f memory flash blankcheck loadbuffer your.hex program verify start reset 0

## How to change the ISP configuration word 

The ISP configuration word is stored in the Flash User Page section. To read this section command:

	%DFU% -operation memory user read savebuffer userpage.hex hex386

- `memory user`, selects the user page for the target memory 
- `read`, reads the target memory into the buffer 
- `savebuffer userpage.hex hex386`, saves the buffer into the file in hex386 format 

The default userpage.hex for UC3A1 looks like this 

	:020000048080FA 
	:10000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF00 
	:10001000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF0 
	:10002000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFE0 
	:10003000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFD0 
	:10004000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFC0 
	:10005000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFB0 
	:10006000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFA0 
	:10007000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF90 
	:10008000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF80 
	:10009000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF70 
	:1000A000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF60 
	:1000B000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF50 
	:1000C000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF40 
	:1000D000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF30 
	:1000E000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF20 
	:1000F000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF10 
	:10010000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF 
	:10011000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEF 
	:10012000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFDF 
	:10013000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFCF 
	:10014000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFBF 
	:10015000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFAF 
	:10016000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF9F 
	:10017000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF8F 
	:10018000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF7F 
	:10019000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF6F 
	:1001A000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5F 
	:1001B000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF4F 
	:1001C000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF3F 
	:1001D000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF2F 
	:1001E000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF1F 
	:1001F000FFFFFFFFFFFFFFFFFFFFFFFF929E2A9E02 
	:00000001FF

The second last line shows the current ISP configuration word. By changing this, you can configure the ISP I/O condition pin and ISP I/O condition level. The pin number tells the ISP which pin to test during the boot process. The pin state will be used to decide whether to start the Bootloader or the uploaded program. The condition level indicates if the Bootloader is started at active high or low. 

The default value of UC3A1 ISP configuration word is *0x929E2A9E*. This means that the state of the GPIO pin number 20 will be used to decide whether the Bootloader or the uploaded program is started. To change this pin, for example, to GPIO pin number 69 the 0x2A part of the configuration word has to be changed to *0x45*. The last byte of the configuration word is the CRC-8 checksum for the previous bytes of the word. So when changing the configuration word you have to calculate a new CRC-8 checksum for that. For *0x929E45* that's *0x94*. Use, for example, this [Java applet for checksum calculations](http://smbus.org/faq/crc8Applet.htm). The Frame Check Sequence...... field of the applet tells the checksum. 

Now use Batchisp to update the ISP configuration word.

	C:\>%DFU% -operation memory user addrange 0x1FC 0x1FC fillbuffer 0x92 program 
	C:\>%DFU% -operation memory user addrange 0x1FD 0x1FD fillbuffer 0x9E program 
	C:\>%DFU% -operation memory user addrange 0x1FE 0x1FE fillbuffer 0x45 program 
	C:\>%DFU% -operation memory user addrange 0x1FF 0x1FF fillbuffer 0x94 program

- `addrange 0x... 0x...`, sets the address range which to program 
- `fillbuffer 0x..`, fills the buffer with the data 

## Reprogramming gerenal-purpose fuse bits 

There are 32 pcs of general-purpose fuse bits in UC3 chips. These bits are located at the configuration memory section. The default value for the UC3A1 is *0xFC07FFFF*. To know which is which, refer to the [doc7745.pdf](http://www.atmel.com/Images/doc7745.pdf).

To read the configuration section into the file, use this command

	%DFU% -operation memory configuration read savebuffer fusebits.hex hex386

The fusebits.hex file will now read as

	:020000040000FA 
	:1000000001010101010101010101010101010101E0 
	:1000100001010100000000000000010101010101D7 
	:00000001FF

How to interprete and represent this in hexadecimal form is left for you as a homework (I don't know the answer;). Maybe someone will comment on this below. However, if you managed to alter the hex appropriately, the fuse bits can be reprogramed with this command

	%DFU% -operation memory configuration loadbuffer fusebits.hex program verify

## Troubleshooting 

### 'batchisp' is not recognized as an internal or external command

	C:\>batchisp -version 
	'batchisp' is not recognized as an internal or external command, 
	operable program or batch file. 

You will get this error message when the installation directory of the Batchisp is not defined in your PATH environmental variable. FLIP installer should have set this, but for some reason it wasn't. A quick solution is to find the Batchisp from your system and update the PATH with its installation directory. Use `dir /s` command to find the batchisp.exe: 

	C:\>dir /s batchisp.exe 
	 Volume in drive C has no label. 
	 Volume Serial Number is 7AAB-DA7D 

	 Directory of C:\Program Files (x86)\Atmel\Flip 3.4.5\bin 

	07.02.2011  15:24           147 456 batchisp.exe 
	               1 File(s)        147 456 bytes

Then update the PATH 

	C:\>set PATH=%PATH%;C:\Program Files (x86)\Atmel\Flip 3.4.5\bin

### Batchisp complains that MSVCR71.dll is missing 

Your system cannot locate this Java related .dll file from the PATH. The solution is simple, find the file from your system and make sure that it is included in the PATH. You should have this file in your system despite the error, as you have the Java JRE installed (or did you skip that part?). 

Again use the `dir /s` command to find the `msvcr71.dll`

	C:\>dir /s msvcr71.dll 
	 Volume in drive C has no label. 
	 Volume Serial Number is 7AAB-DA7D 

	 Directory of C:\Program Files (x86)\Java\jre6\bin 

	23.02.2012  11:37           348 160 msvcr71.dll 
	               1 File(s)        348 160 bytes

Now either update the path with the directory where the `msvcr71.dll` is located or copy it into the `bin\` folder under the FLIP installation directory. (Yes, I had an old JRE version at the time of writing.) 

### WARNING: The user program and the bootloader overlap! 

If this happens you are most probably using the trampoline technique to jump over the Bootloader section in your program. This is what Atmel does in their example programs. As far as everything works you may ignore the warning. &#10065;