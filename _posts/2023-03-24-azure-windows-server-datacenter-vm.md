---
layout: post
title: "Create an Azure Windows Server Datacenter VM"
author: tom
tags: [azure]
comments: true
---

Create a Windows Server 2022 Datacenter Azure Edition virtual machine.

## Create an Azure Windows Server Datacenter VM

Change the size settings to your desire configuration. The settings below are for testing/trying things out.

In Azure, search for `Virtual Machine`

* Click Create
* Select the resource group
* Enter a name
* For `Image` click `See all images` or select the latest `Windows Server 2022 Datacenter Azure Edition`
* Choose the lowest size with 4GB like `Standard B2s -2 vcpus, 4GiB`
* Enter user/pwd
* Public inbound ports. Keep RDP
* On `Disks` tab select `Standard SSD`
* On `Networking` tab
  * Select the virtual network
  * Subnet `default`
* Click `Review and create`
* Select `Create`

After provisioned:

* Go to networking
* Select RDP
* Change `Source` to `My IP Address` (Or select a different option)


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)