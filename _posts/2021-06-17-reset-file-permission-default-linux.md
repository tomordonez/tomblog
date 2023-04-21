---
layout: post
title: "Reset File Permission to Default in Linux"
description: "Reset File Permission to Default in Linux"
author: tom
image: assets/images/1.jpg
tags: [linux, chmod]
---

How to reset a file permission to the default in Linux.

The default permission for files in Linux is:

    -rw-rw-rw-

If it was something else like read, write, execute for everyone like:

    -rwxrwxrwx

You can change it to the default creepy version:

    $ chmod 666 your-file
    $ ls -l
    -rw-rw-rw-