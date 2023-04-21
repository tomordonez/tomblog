---
layout: post
title: "Remote Access Linux Fedora Desktop in LAN"
redirect_from:
  - /remote-access-linux-fedora.html
categories: [Linux]
image: assets/images/3.jpg
tags: [sysadmin, linux, fedora]
---

A mini tutorial about: **remote access in Linux**.

This works if you have another computer in your LAN running a Linux machine, either Fedora or Ubuntu.

## 1. Enable Remote Access in the host

On the host computer. Go to Settings > Sharing.

Turn **Sharing** to `ON`. Then **Remote Login** to `ON`.

Find the IP of this machine.

## 2. Connect Remote from a guest in the same network

From another computer's terminal. Type the user and IP of the host.

    ssh user-at-host@IP-of-host

It will ask to accept the signature and to login using such user.

Exit the session with `exit`


