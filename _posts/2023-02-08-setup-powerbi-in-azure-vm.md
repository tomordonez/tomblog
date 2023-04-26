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
 
## Create a Virtual Machine in Azure

Follow this post [Create a Windows 10 Virtual Machine in Azure](../create-windows10-vm-azure/) to create a Virtual Machine in Azure.

## Install PowerBI on the VM

On the VM, open Edge.

Go to [https://powerbi.microsoft.com/en-us/desktop/](https://powerbi.microsoft.com/en-us/desktop/). If you click the default Download option, it will open the Microsoft store and it asks you to login. Instead, select download options. 

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