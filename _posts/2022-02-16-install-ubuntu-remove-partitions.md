---
layout: post
title: "Install Ubuntu and Remove Partitions"
description: "How to install Ubuntu on a computer that had another OS with partitions"
author: tom
image: assets/images/1.jpg
tags: [linux, ubuntu, partitions]
comments: true
---

How to install Ubuntu on a computer that had another OS with partitions.

I had a computer with Fedora 30. I tried to update to 35 but it wouldn't work. It said to update to the next version 31 but it also failed with low space on root and boot. I decided to replace it with the latest version of Ubuntu. I already had another computer with the latest Fedora version.

When trying to install Ubuntu from a USB and setting up the partitions, the delete (minus sign) button was disabled to remove current partitions. Also, selecting the New Partition Table on `sdb` which is the disk I wanted to use, said that the lvm for this disk was used by fedora group, which was a warning with only an OK button.

This post on askubuntu [here](https://askubuntu.com/questions/228136/how-to-remove-all-lvs-vgs-and-partitions-on-all-drives-before-installing-12-04) said to run two commands on the terminal while in the install process, however I couldn't access the terminal using the shortcut.

* Go to the Ubuntu website and download the ISO
* Load the ISO into a USB. In Fedora this can be done with Fedora Media Writer.
* Connect the USB to the computer, enter the BIOS and boot with USB.
* Enter Live mode.

Go to the terminal and run:

	sudo vgdisplay
	sudo vgremove <groupname>

On the desktop double click the install icon.

* Follow steps. For installation type, select Something else.
* In partition selection: Select partition to remove and New Partition Table

For a 250GB SSD this is how I partitioned:

* root 100GB, ext4
* boot 16GB, ext4
* home 100GB, ext4
* swap 32GB, swap area

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)