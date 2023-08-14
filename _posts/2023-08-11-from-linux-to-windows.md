---
layout: post
title: "Moved from Linux to Windows"
description: "After being a long time Linux user, I moved to Windows"
author: tom
image: assets/images/1.jpg
tags: [linux, windows]
comments: true
---

After being a long-time Linux user, I moved to Windows.

Last year I wrote about moving [from Fedora to Ubuntu](../from-fedora-to-ubuntu/). I've been a loyal Fedora user for two years and moved to Ubuntu due to a lack of support for audio files for my hardware. My audio in Fedora worked fine but not great. After I moved to Ubuntu, my audio worked great. However, in the last few months, GNOME started crashing more often, and I resorted to a weird hack when it wouldn't recognize my second monitor.

## Other reasons for moving to Windows

I have been running Windows in Azure VMs, which was great until I got a high Azure bill. Then I started tracking my Azure usage more closely, reviewing weekly billing forecast reports, and removing things I wasn't using.

The highest cost was storage inside the VMs, and I couldn't easily change the storage size. When creating the VM, it doesn't let you choose the size of the OS disk. The workaround was that after deploying the VM, you could re-partition the OS in Disk Management, similar to installing Windows on a local computer. Then you could reduce the disk size and Azure detected less disk usage. However, I found this "hack" annoying after deploying a VM.

One last reason to move to Windows was that I registered for a class that requires C# and Unity installed. I wanted the native experience and not the Linux or Azure VM workarounds. I could run Windows inside Linux or install Unity in an Azure VM, but I  wanted a friendlier experience.

## Things I miss from Linux

The terminal/CLI in Linux is a great experience. However, I am now using Bash inside VScode in Windows 10. It has a bit of a lag, but it's ok.

I love GNOME. Until it started crashing. Mostly my fault for having such old hardware. Installing Linux is a very liberating experience. The OS is simple and easy to use.

DNF in Fedora and APT in Ubuntu were great package managers. However, Snap didn't work well. It would restart my computer without notifying me, and I couldn't find a fix (that didn't require hours of research).

You can install Linux bare bones with only the things you need. Compared to Windows. I don't need Cortana, Mail, Maps, two media players, Paint, Photos, Skype, Spotify, Weather, Video editor, Xbox, etc.

Other Linux flavors I tried in the past:

* Kali
* Red Hat
* Kubuntu
