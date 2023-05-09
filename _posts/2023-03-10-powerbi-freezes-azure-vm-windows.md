---
layout: post
title: "**Unsolved**: Power BI freezes on Windows Azure VM"
author: tom
tags: [azure, power bi]
comments: true
---

**Unsolved**: Power BI freezes in a Windows Azure VM when connecting RDP from Linux using Remmina.

Setup:

* Azure Windows 11 VM or
* Azure Windows 10 VM
* Linux Ubuntu with Wayland or X11
* Remmina to RDP from Linux to Azure VM

Steps to Reproduce:

1. Connect RDP from Linux to Azure VM with Remmina
2. Open a Power BI report
3. Enable Pause visuals
4. Move to another application outside of Remmina
5. Go back to Remmina
6. Power BI is frozen.
7. The mouse works: if you click on Windows time/date, if you click Microsoft Edge (if pinned on the bar) but you cannot see the window that opened, if you click on the Windows 11 logo or search (but they won't open). You can click on Powershell (if pinned on the bar) and it opens where you can kill the Power BI process.

Things I tried:

* I thought it was a Windows 11 problem. But I switched to Windows 10 and the issue remained.
* I never had a problem with Remmina RDP from Linux to Windows VM. I thought the problem wasn't with Linux, the Windows VM, or Remmina.
* The only thing I changed in my workflow was in Power BI, enabling the setting `Pause visuals`

**Possible root of the problem: Power BI - Pause Visuals**

When using Power BI pause visuals while connected to RDP using Remmina. The graphics of the Windows VM freeze, even though you can move the mouse and click things, applications won't open or do anything. It leaves you with two options:

* If you can open Powershell. Kill the Power BI process
* Restart the VM

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)