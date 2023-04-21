---
layout: post
title: "Fedora Upgrade Version"
description: "Upgrade version of Fedora Linux"
author: tom
image: assets/images/1.jpg
tags: [linux]
---

Commands to upgrade version of Fedora Linux.

First I removed a repo I wasn't using. List all repos:

    $ ls /etc/yum.repos.d/

I disabled the `virtualbox` repo:

    $ sudo dnf config-manager --set-disabled virtualbox

Upgraded to version 34.

    $ sudo dnf upgrade --refresh
    $ sudo dnf install dnf-plugin-system-upgrade
    $ sudo dnf system-upgrade download --releasever=34
    $ sudo dnf system-upgrade reboot

If upgrading shows errors about old packages:

    $ sudo dnf system-upgrade download --releasever=34 --skip-broken