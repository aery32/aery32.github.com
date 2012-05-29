---
layout: post
title: "Go ahead with AVR Toolchain version 3.4.0"
description: "How to use AVR Toolchain included in AVR Studio 6 as a standalone version."
author: Kim Blomqvist
author-email:

tags:
- "AVR Toolchain"

categories:
- article

level: advanced

tweet-text: "Go ahead with AVR Toolchain version 3.4.0 via @aery32devzone"

summary: "Atmel released a new version of its AVR Toolchain (3.4.0) with AVR Studio 6 a week or two ago. However, the standalone installer of this toolchain has not been released yet. This post shows how to go ahead with the new version that is included in Studio 6"
---

Atmel released a new version of its AVR Toolchain (3.4.0) with AVR Studio 6 a week or two ago. However, the standalone installer of this toolchain has not been released yet. This post shows how to go ahead with the new version that is included in the Studio 6. For the curiosity, let's check what version of the toolchain we have already installed. This can be done via command line. Assuming that you are on Windows, press Win-R and type `cmd`, then press enter.

<span class="label label-info">Heads up!</span> Unfortunaltely, Linux users have to wait standalone package, because Studio 6 is only for Windows.

In command line type

    avr32-gcc --version

so you should see something similar

![Checking the AVR Toolchain version](/images/avr_toolchain_what_version.png "Checking the AVR Toolchain version")

Seems like I have the latest standalone version that's 3.3.2 at the moment of writing. Now let's update this to the latest version, so download and install [AVR Studio 6](http://www.atmel.com/tools/atmelstudio.aspx). If you installed it into the default folder, you can find the toolchain from the `C:\Program Files (x86)\Atmel\Atmel Studio 6.0\extensions\Atmel\AVRGCC\3.4.0.65\AVRToolchain`. 

Go back to the command line and reset the PATH so that it overrides the current version (3.3.2) of the AVR Toolchain. That's prepending the PATH variable with the installation dir that includes the binaries of the latest toolchain version

    set PATH=C:\Program Files (x86)\Atmel\Atmel Studio 6.0\extensions\Atmel\AVRGCC\3.4.0.65\AVRToolchain\bin;%PATH%

 ![AVR Toolchain version 3.4.0](/images/avr_toolchain_3_4_0.png "AVR Toolchain version 3.4.0")

 If you recheck the toolchain version now, you can see that it's updated to 3.4.0. The drawback with this method is that you have to always remember to reset the PATH or otherwise the old version of the toolchain is reverted. Thus open the *Control Panel &raquo; System and Security &raquo; System*, then select *Advanced system settings*. From the window shown below press *Environment Variables...*. Then you can change the PATH variable in the way that's persistent.

![System Properties window in Windows 7](/images/win7_system_properties.png "System Properties window in Windows 7")

 &#10065;
