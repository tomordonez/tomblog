---
layout: post
title: "RDP from Linux to an Azure VM"
author: tom
tags: [azure, rdp, linux, virtual machine]
comments: true
---

How to RDP from Linux to an Azure Virtual Machine.

## Install an RDP program in Linux

Install an RDP program to connect to the Windows VM.

An option is `Remmina` as seen in the docs [here](https://remmina.org/how-to-install-remmina/).

Install using snap

    sudo snap install remmina

Output (Feb 2023):

    remmina v1.4.29 from Remmina Upstream Developers

Open the program

* Create an RDP connection
* Enter a name
* Protocol select `RDP`
* Server: Enter the public `IP address` of the Azure VM
* Enter user and pwd
* Resolution: `Use client resolution`
* Click `Save and Connect`

## Troubleshooting

* If it doesn't connect, go to the VM, then `Networking`
* Select the RDP inbound port rules
* Check that the `Source` is selecting your IP numbers where you are connecting from.
* For this tutorial I selected `My IP address`

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)