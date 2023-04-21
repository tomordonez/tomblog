---
layout: post
title: "Azure VM agent status is not ready"
author: tom
tags: [azure, azure VM]
comments: true
---

Once in a while I am getting this issue when starting a VM.

    Virtual machine agent status is not ready.

**VM details**

* OS: Windows 10 Pro
* VM generation: V2
* VM architecture: x64
* Agent version: 2.7.41491.1075
* Size: Standard B2ms
* vCPUs: 2
* RAM: 8GiB
  
**Steps to reproduce**

* Go to Azure Portal
* Go to the VM and click Start
* On the client app, connect with RDP
* The app shows `Lost connection to the RDP server`
* On the VM, click on `Refresh`
* A message says `Virtual machine agent status is not ready.`
* On the VM, under `Properties`, the `Agent status` shows as `Not Ready`

## Troubleshooting the VM

The message has an option to troubleshoot the problem.

Scroll down to `Diagnostics`.

Under `Agent health diagnostic`, click `Rerun diagnostics`. Then got this message:

    The diagnostic did not detect any issues. This it may indicate that the diagnostic was unable to properly assess the issue. In such cases, it may be necessary to explore other troubleshooting methods or seek support from Microsoft to resolve the guest agent issue.

Under `Operation failure diagnostic`, click `Rerun diagnostics`. Same message.

## Restart the VM

If nothing changes. Try restarting the VM.

## Refresh the VM

Magically the `Agent status` changed to `Ready`. Tried again to connect with client using RDP and it connected to the VM.

## Wait 5 minutes

The new behavior I am seeing is that the VM doesn't start right away. I guess, similar to a computer when you turn it on. It doesn't magically jumps to the login screen. For the VM, once I start it, I wait around 5 minutes, use the `refresh` button, and the `Agent status` changes to `Ready`.

![Azure VM Agent Status Not Ready](/images/azure-vm-agent-not-ready.png)

## Solution

Unknown. But possibly, once the issue appears:

* Go to the Troubleshooting link
* Rerun both diagnostics `Agent health` and `Operation failure`
* Refresh the VM
* Restart the VM
* Refresh again