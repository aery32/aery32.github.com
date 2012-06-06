---
layout: post
title: "Use Git to fetch Aery32 Software Framework"
description: ""
author: "Kim Blomqvist"
author-email:

tags:
- "git"

categories:
- article

level: beginner

tweet-text: "Use Git to fetch Aery32 Software Framework #aery32devzone"

summary: "The development process of Aery32 Software Framework takes place in GitHub that uses Git version control system. Although you would not use Git for version controlling, its clone command is a convenient way to download project files to your workspace"
---

<div style="margin: 15px 0; font-size: 10px;">
<img style="margin-bottom: 5px;" itemprop="image" src="{{ site.url }}/images/vendor/Git-Logo-2Color.png" alt="Git logo" />Git Logo by Jason Long is licensed under the Creative Commons Attribution 3.0 Unported License.
</div>

The development process of [Aery32 Software Framework](https://github.com/aery32/aery32) takes place in GitHub that uses [Git](http://git-scm.com/) version control system. Although you would not use Git for version controlling, its clone command is a convenient way to download projects files to your workspace. Just cast the spell and let the magic happen

    git clone git://github.com/aery32/aery32.git

Humm... but how?
----------------

Well first you have to [download and install Git](http://git-scm.com/downloads). There are installers for Mac OS X, Windows, Linux and Solaris. Pick up the one for your operating system &ndash; Linux users can also install Git via package managers such as apt-get and yum. If you are unsure what to select on each screen during the installation, check the [installation instructions from GitHub](https://help.github.com/articles/set-up-git#platform-windows). Be sure to click "*Not sure what to pick on each screen?*" to see screenshots. It's also recommended to configure your Git settings after the installation as instructed in GitHub.

Now we are ready to use Git. In Windows we will use Git Bash. You can find this from your Start menu, or you can open it via Windows Explorer (press Win-E for quick access). Let's open it via Windows Explorer. Browse to `My Documents` and create a new folder called `Workspace` there. Then right click that folder and select *Git Bash Here*. Git Bash command line window opens. Now type the command

    git clone git://github.com/aery32/aery32.git

When done you have a new directory called `aery32` under your Workspace. That's it, you are now ready to work under that directory &ndash; open `src/main.c` to start programming.

<span class="label label-info">Tip!</span> If you want to Git clone Aery32 Framework into some other directory than `aery32` you can append the Git clone command with the desired directory name:

    git clone git://github.com/aery32/aery32.git myproject

<span class="label label-info">Tip!</span> You can remove the `.git` directory and the file named as `.gitignore`, if you don't like those.

&#10065;
