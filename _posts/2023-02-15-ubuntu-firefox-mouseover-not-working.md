---
layout: post
title: "Ubuntu Firefox Mouseover Not Working"
description: "How to fix this in Ubuntu Firefox when the mouseover stops working"
author: tom
image: assets/images/1.jpg
tags: [Linux, Ubuntu, Firefox]
comments: true
---

How to fix this in Ubuntu Firefox when the mouseover stops working.

**Steps to reproduce**

* Unknown
* Using the mouse to navigate the Firefox UI
* Mouseover suddenly stops working on the UI and on web pages
* Only way to fix was to restart Firefox

**Environment**

* Ubuntu 22.04, Wayland
* Firefox
  * 109.0.1 (64-bit)
  * Installed with Snap

As seen in [this](https://bugzilla.mozilla.org/show_bug.cgi?id=1774531) bugzilla bug report and [this](https://help.ubuntu.com/community/EnvironmentVariables) Ubuntu doc.

Add this to `~/.profile`

	export MOZ_ENABLE_WAYLAND=1

Logout and login again. The problem should be gone.