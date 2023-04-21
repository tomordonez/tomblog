---
layout: post
title: "Ubuntu Displayport Not Working With Second Monitor"
description: "A second monitor won't work on Displayport on Ubuntu"
author: tom
image: assets/images/2.jpg
tags: [linux, display, monitors]
comments: true
---

A second monitor won't work on Displayport on Ubuntu.

Setup:

* Thinkpad T430
* Ubuntu 22.04
* GNOME 42.5
* Wayland
* Graphics: NVC1/Mesa Intel HD Graphics 4000
* 1st monitor with VGA/VGA cable
* 2nd monitor with Displayport/HDMI cable

...and suddenly one day my 2nd monitor stopped working. No signal. Not recognized. I initially had an HDMI to Displayport adapter. After researching, I read that it could be the adapter. I changed the adapter with a Displayport to HDMI cable. Still nothing.

I have seen that others used `xrandr` to see if `DP` was listed. Mine didn't list anything that said `DP`.

On a Lenovo forum, someone commented that they had Red Hat and they switched the BIOS display from Nvidia Optimus to Discrete. I did that and also disabled the auto detect/auto switch to Optimus. However, when I did that change and restarted, the display on the laptop went blank. I went back to the BIOS and switched the 'auto detect Optimus' to enable and restarted. Still nothing, blank screen. Changed the BIOS to the original setting. Restarted and the 2nd monitor was now recognized again.

**Weird hack solution**

1. Go to the BIOS and change the Display to Discrete, reboot.
2. The screen goes blank, hard restart.
3. Go to the BIOS again and change back to Nvidia Optimus, reboot.
4. Solved.

## Ubuntu Gnome Shell Crashes and Restarts

Often my Ubuntu crashes and restarts. When the error popup opens it says it crashed due to Gnome shell.

A similar issue was reported to Ubuntu [here](https://bugs.launchpad.net/ubuntu/+source/mutter/+bug/1969422). And the thread says that there is a package called `mutter` that resolves the issue

    sudo apt install mutter

Output:

    The following NEW packages will be installed:
    mutter
    0 upgraded, 1 newly installed, 0 to remove and 26 not upgraded.
    Need to get 107 kB of archives.
    After this operation, 239 kB of additional disk space will be used.
    Get:1 http://us.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 mutter amd64 42.5-0ubuntu1 [107 kB]
    Fetched 107 kB in 0s (299 kB/s)  
    Selecting previously unselected package mutter.
    (Reading database ... 211747 files and directories currently installed.)
    Preparing to unpack .../mutter_42.5-0ubuntu1_amd64.deb ...
    Unpacking mutter (42.5-0ubuntu1) ...
    Setting up mutter (42.5-0ubuntu1) ...
    update-alternatives: using /usr/bin/mutter to provide /usr/bin/x-window-manager 
    (x-window-manager) in auto mode
    Processing triggers for desktop-file-utils (0.26-1ubuntu3) ...
    Processing triggers for gnome-menus (3.36.0-1ubuntu3) ...
    Processing triggers for mailcap (3.70+nmu1ubuntu1) ...

The bug report says that:

    I am unable to confirm if that's the same crash but the package you need to get the fix is 'libmutter-10-0' https://launchpad.net/ubuntu/jammy/amd64/libmutter-10-0/42.5-0ubuntu1