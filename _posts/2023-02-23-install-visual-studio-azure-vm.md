---
layout: post
title: "Install Visual Studio in Azure VM"
author: tom
tags: [azure, azure vm, visual studio]
comments: true
---

Installing Visual Studio in an Azure VM.

* Create an Azure VM
* Install Visual Studio
* Install Analysis Services extension
* Install SQL Server

## Create an Azure VM

Create an Azure VM. See details in [Create a Windows 10 Virtual Machine in Azure](../create-windows10-vm-azure/)

Start the VM and connect using RDP.

## Download Visual Studio

Inside the Windows VM. Download and install Visual Studio Community edition.

When running the install, select package `Data storage and processing` (Total space required `4GB`)

## Install Microsoft Analysis Services

Open Visual Studio and `continue without code`.

* Menu/Extensions/Manage Extensions
* Visual Studio Marketplace
  * Search for `analysis`
  * Select `Microsoft Analysis Services Projects`
  * It will download a `VSIX` file. Open this file.
  * Continue to Install
  * If it asks to end processes. Instead, close Visual Studio.

Start Visual Studio again
* Continue without code
* File
* New Project
* Type `analysis`
* The option `Analysis Services Tabular Project` should be listed.
  * This project type won't run without SQL Server backend

## Install SQL Server

* Search online for `SQL server express`
* Download the latest version from Microsoft
* Install using `Basic`
* After installing then `Install SSMS` (SQL Server Management Studio) to use the UI
  * It will install SSMS + Azure Data Studio