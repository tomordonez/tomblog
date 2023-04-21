---
layout: post
title: "Set up PowerBI in an Azure VM"
description: "How to set up Power BI desktop in an Azure Virtual Machine."
author: tom
image: assets/images/1.jpg
tags: [powerbi, azure]
comments: true
---

How to set up Power BI desktop in an Azure Virtual Machine.

On my quest to get some Azure certifications, I learned how to do this.

**Problem**

* You don't have a Windows machine to install PowerBI (You are on a Mac or Linux)
* Or your computer is too slow to build your reports

**Solution**

* Run PowerBI from an Azure VM
* Increase the VM power based on your needs
* Shutdown the VM when you are done using it

## Estimate Azure VM pricing

Open the Azure pricing calculator [here](https://azure.microsoft.com/en-us/pricing/calculator/)

Select `Virtual Machine` and scroll down to see the calculator.

You can have a decent experience running PowerBI with 8GB of RAM. Perhaps 4GB of RAM since you will only run PowerBI. However, if you load a large dataset locally, you will need at least 8GB.

On the calculator, leave the defaults:

* `Region`: Central US
* `OS`: Windows
* `Type`: OS Only
* `Tier`: Basic
* `Instance`: A2: 2 Cores, 3.5GB RAM, 60GB Temporary Storage, $0.085/hour
* `Virtual machine`: 1
* `Number of hours`:
  * Let's say that you work on PowerBI 4 hours per day, 5 days a week, 4 weeks. Total hours `80`
  * Monthly cost so far is `$9.20/month`
* `Compute (A2)`: Pay as you go
* `OS (Windows)`: License included

Changing the instance to A3 with 7GB gives `$17.44/month`

However, when creating a VM below, there aren't `A` instances. The lowest letter is `B`.
 
## Create a Virtual Machine in Azure

Go to your Azure dashboard. (Or open a free Azure account)

In the search, type `virtual machine` and select the service.

On the drop down, click `Create` and then `Azure virtual machine`

**Project details**

* Create a new resource group `powerbi-vm`

**Instance details**

* `Name`: Enter `powerbi`
* Select the `region` closest to you.
* `Availability options`: Leave default `No infrastructure redundancy required`
* `Security type`: Leave default `Standard`
* `Image`: Select `Windows 10 Pro, version 21H2 - x64 Gen2 (free services available)`
* `VM architecture`: By default it should be `x64`
* `Size`
  * The default is too expensive `Standard_D2s_v3 - 2vcpus, 8GiB memory ($96.36/month)`
  * Click `See all sizes`
    * Review the prices for `B-Series` (they are the cheapest)
      * `B2s` ($30.37/mo) has 2 vcpus, 4GB RAM, 8GB temp storage
      * `B2ms` ($60.74/mo) has 2 vcpus, 8GB RAM, 16GB temp storage
  * Select `B2s`

**Admin account**

* Create `username`
* Create `password`

**Inbound port rules**

* `Public inbound ports`: Allow selected ports
  * Select `RDP` and `HTTPS`
  * (This will allow all IP addresses to access your VM. This is only recommended for testing. Use the Advanced controls in the Networking tab to create rules to limit inbound traffic to known IP addresses)

**Licensing**

Check `I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights`

Click on `Next: Disks`

**Disks/OS Disk**

* `OS disk type`: Change to `Standard SSD`

Click on `Next: Networking`

**Networking**

Leave defaults.

Click on `Review + create`

**Review + create**

Review all details then click `Create`.

When ready click `go to resource`

## RDP on Linux

If you have a Linux machine, install an RDP program to connect to the Windows VM.

An option is `Remmina` as seen in the docs [here](https://remmina.org/how-to-install-remmina/).

Install using snap

    sudo snap install remmina

Output (Feb 2023):

    remmina v1.4.29 from Remmina Upstream Developers

Open the program

* Create an RDP connection
* Enter a name like `azure-pbi`
* Protocol select `RDP`
* Server: Enter the `IP address` of the VM (see below)
* Enter user and pwd
* (Optional) Select a shared folder
  * Create a folder in `home` called `azure-pbi`
  * Enter this location `/home/azure-pbi`
* Resolution: `Use client resolution`
* Click `Save and Connect`

## Connect to the VM

Go to the VM, then `Connect` drop down, select `RDP`.

If you are on Windows or OSX. Click on `Download RDP File` to connect to the VM.

If you are on Linux, the IP number of the VM is on this screen, then follow above steps.

If it asks to accept the certificate, select `Yes`.

The Windows VM should start loading as if you just bought a new Windows laptop.

Choose privacy settings for your device. Set everything to `No` and `Accept`. Networks popup select `No`

## Install PowerBI on the VM

On the VM, open Edge.

Search for PowerBI.

Go to [https://powerbi.microsoft.com](https://powerbi.microsoft.com). If you click the default Download option, it will open the Microsoft store and it asks you to login. Instead, select download options. 

Select the language. Then select the `_x64.exe` file.

Open the file and follow the default prompts to install. Then start PowerBI.

## Test PowerBI with a dataset

Go to Edge and search for `stack overflow survey`. Go to the current year and download the full dataset.

In the Downloads folder, extract the zip file. The directory has a size of ~ 100MB.

Go to PowerBI. Get Data.

Select `File` then `Text/CSV` then `Connect`. Open the schema file, then the dataset file.

Click the `Clustered Barchart`

Add fields to X and Y axis to see the data.

## Shut down the VM

When done working on PowerBI.

* Shut down the machine in Windows
  * Go to the bottom bar, shut down.
  * The RDP connection should close.
* Go to Azure
  * Find the VM and click `Stop`.
  * The status **MUST** appear as `Stopped (deallocated)` (it takes a few seconds)

As seen in [this](https://build5nines.com/properly-shutdown-azure-vm-to-save-money/) article, it is recommended to auto shutdown the VM in case you forget to do it.

* Go to Azure
* Find the VM
* Left menu Operations
* Enter a time to shutdown, then Save.

## Connecting Again

When you shut down the machine:

1. Start the VM again
2. Connect using RDP connection
3. Windows starts
4. Work on PBI
5. Repeat shutdown process