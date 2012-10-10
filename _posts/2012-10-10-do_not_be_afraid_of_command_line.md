---
layout: post
title: "Don't be afraid of the command line"
description: "The use of command line is not any harder than the use of other tools"
author: "Muiku Oy"
author-email:

tags:
- "command line"

categories:
- article

label: "point of view"

tweet-text: "Don't be afraid of command line #aery32devzone"

summary: "It probably can't be denied that the command line has a repulsive reputation and possibly scares the beginners away to look for other platforms. But, after all it is not any harder to use than other tools and beginners do not often need to be very highly skilled in order to be successful."
---

<a href="/images/cmd-prompt.png" title="Windows command prompt">
<img class="pull-right" width="50%" itemprop="image" src="{{ site.url }}/images/cmd-prompt.png" alt="Aery32 Framework compiled on the windows command prompt" /></a>

There has been some questions and inquiries to clarify to whom the Aery32 board is intended for. It appears that the use of command line in the [Aery32's Quick start guide](http://www.aery32.com/pages/quick-start) has seen inconvenient and scary, especially from the perspective of beginners. Briefly, the Quick start guide instructs a user to download the sources of the framework and then advises to use the framework's Makefile straight from the command line to get the "blinking LED" programming tutorial compiled and uploaded to the board.

It probably can't be denied that the command line has a repulsive reputation and possibly scares the beginners away to look for other platforms. But, after all it is not any harder to use than other tools and __beginners do not often need to be very highly skilled in order to be successful__. So taking advanced users into account as well, we'd like to leave the Quick start as it is with the command line. We feel it more comprehensive that way. However, this does not mean that the development is meant to happen completely from the command line. In fact, quite the contrary is true. After getting the "blinking LED" demo working, the __user should adjust his or her development environment__, which pretty much means setting up an editor of your preference.

Aery32 framework is not restricted to any specific integrated development environment (IDE) or editor. We respect your taste. The best one is the one you like best and to figure out which one you like best you have to try a few. We like very much [Sublime Text 2](http://www.sublimetext.com/2) and thus the framework comes with a preset project-file for this editor. In practice, [the use of Aery32 framework from Sublime Text 2](http://aery32.readthedocs.org/en/latest/use_with_st2.html) is as easy as pressing __Ctrl+Shift+B__. This compiles the program and uploads it into the board. No need for the command line at all. And if you prefer Eclipse, we have made instructions [how to import Aery32 framework to Eclipse Juno](http://aery32.readthedocs.org/en/latest/use_with_eclipse.html).

In future, we have planned to build a plug-in for Sublime Text 2 (and possibly for Eclipse as well) which sets up the editor and involves the functionality to create new projects, update to the newest version of the Aery32 framework, program the board etc. After then, instead of manually downloading the framework and setting up the editor, the setup process would be as simple as installing the plug-in in Sublime Text 2.