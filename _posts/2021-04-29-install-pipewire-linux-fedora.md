---
layout: post
title: "Install Pipewire on Linux Fedora"
description: "Installing Pipewire on Linux Fedora"
author: tom
image: assets/images/1.jpg
tags: [linux, music]
comments: true
---

Installing Pipewire on Linux Fedora.

The sound was much better installing Pipewire. Before Pipewire I always thought the sound was shallow and flat.

I upgraded to Fedora 34 but it seemed like Pipewire wasn't working. I checked with this:

    $ pactl info

With output:

    Server Name: PulseAudio

The Pipewire documentation says that it needs to show:

    Server Name: PulseAudio (on PipeWire 0.3.X)

I checked that Pipewire was installed. This produced an output:

    $ pipewire --version

I checked this but it showed it was already installed:

    $ sudo dnf install pipewire

I was missing this package:

    $ sudo dnf install --allowerasing pipewire-pulseaudio

Start the service:

    $ systemctl --user start pipewire-pulse.service pipewire-pulse.socket

Output:

    Server String: /run/user/1000/pulse/native
    Library Protocol Version: 34
    Server Protocol Version: 35
    Is Local: yes
    Client Index: 127
    Tile Size: 65472
    User Name: tom
    Host Name: dev
    Server Name: PulseAudio (on PipeWire 0.3.26)
    Server Version: 14.0.0
    Default Sample Specification: float32le 2ch 48000Hz
    Default Channel Map: front-left,front-right

**Issues**

I am having an issue with the volume bar. I can move the bar to the left, but it will go mute at this point, and not decrease the volume. Not sure if this is a Pipewire issue.

![Fedora 34 Pipewire volume problem]({{ site.baseurl }}/assets/images/fedora-pipewire-volume-bar-problem.png)

Sources:

* [Pipewire on Fedora](https://fedoraproject.org/wiki/Changes/DefaultPipeWire)
* [Pipewire on Wikipedia](https://en.wikipedia.org/wiki/PipeWire)
* [Pipewire tutorial on Youtube](https://www.youtube.com/watch?v=HHEnaVUnRVM)