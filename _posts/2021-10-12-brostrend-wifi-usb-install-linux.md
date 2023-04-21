---
layout: post
title: "BrosTrend Wifi USB Install in Linux"
description: "Installing BrosTrend Wifi USB in Linux and troubleshooting."
author: tom
image: assets/images/1.jpg
tags: [linux]
comments: true
---

Installing BrosTrend Wifi USB in Linux and troubleshooting.

I bought an Wifi USB from BrosTrend that comes with a big antenna. It might be 12 inches. Had some trouble installing on Linux so here is the tutorial:

As seen on [this](https://github.com/morrownr/8821cu) open source install repo. It has instructions for every distro.

For Fedora:

	sudo dnf -y install git dkms kernel-devel kernel-debug-devel

Then it's the same for all:

	mkdir src
	cd src/
	git clone https://github.com/morrownr/8821cu.git
	cd src/8821cu
	cd 8821cu/
	sudo ./install-driver.sh

This will build the install from source. Then it will ask if you want to edit the driver. To make it easy. Answer `N`. Then it will ask to reboot. Answer `Y`.

## Troubleshooting

Updating your system might undo this install. Such as `sudo dnf update` and rebooting. Then the install above seems to disappear.

Go to the repo:

	cd src/8821cu/
	sudo ./remove-driver.sh 

Reboot. Then:

	cd src/8821cu/
	sudo ./install-driver.sh

Reboot again.


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)