---
layout: post
title: "Create a SQL Server Windows VM"
author: tom
tags: [azure, sql server, virtual machine]
comments: true
---

How to create a Virtual Machine in Azure with Windows SQL Server.

## Create a SQL Server Windows VM

In Azure, search for `Azure SQL`.

Change the size settings to your desire configuration. The settings below are for testing/trying things out.

* Click Create
* On `SQL virtual machines` select an image such as `Free SQL Server License: SQL Server 2022 Developer on Windows Server 2022`
* Click Create
* Select a resource group
* Enter a name like `sqlserver`
* Choose the lowest size with 4GB like `Standard B2s -2 vcpus, 4GiB`
* Enter user/pwd
* Public inbound ports. Keep RDP.
* Licensing
  * Don't select `Would you like to use an existing license?` if you don't have one.
* In `Disks` tab select `Standard SSD`
* In `Networking` tab
  * If using a specific virtual network
    * Select the virtual network
    * Subnet `default`
* In `Management` tab
  * Enable `Auto-shutdown` if you want to make sure to stop the VM every day.
* In `SQL Server Settings` tab
  * SQL Connectivity (If using a private vnet: `Private (within Virtual Network)`)
  * Port `1433`
  * SQL Auth set to `Enable`
    * The default is to login using the same VM credentials
* In `Storage configuration` tab
  * Leave default for an actual prod env
  * For this blog post I lowered the settings.
  * Change configuration
  * Data storage, `Disk type` change to a lower setting like `16 GiB`
  * Log storage, change to `share the data drive for log`
* The `SQL Server License` should be `No`
* Click `Review and create`
* Select `Create`

After provisioned:

* Go to networking
* Select RDP
* Change `Source` to `My IP Address` (Or select a different option)

## Load a sample database in SQL Server VM

RDP to the SQL Server VM and download the sample database.

Go to the Adventure Works Microsoft website [here](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms) and download the latest `AdventureWorksDW` file. For this tutorial I downloaded `AdventureWorksDW2019.bak`.

* Copy the `bak` file to this directory `C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup`
* Open SSMS
* Connect to `localhost`
* Right click `Databases` then `Restore Database`
* Select `Device` and browse to the `bak` file
* Click OK
* Then the `Database` field should populate the name `AdventureWorksDW2019`
* Click OK to restore
* Close SSMS
* Close the RDP connection to the SQL Server

## Use choco to install programs with Powershell

See [Use Chocolatey in Windows Powershell to Install Programs](../chocolatey-windows-powershell-install-programs/)

Optionally install:

* SSMS
* Notepad++
* Azure Data Studio

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)