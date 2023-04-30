---
layout: post
title: "Azure Data Factory Self-Hosted Integration Runtime"
author: tom
tags: [azure, azure data factory]
comments: true
published: true
---

Connect an on-prem SQL Server to Azure Data Factory using a self-hosted integration runtime (IR).

## Summary

This simulates an on-prem environment by creating two servers in the same network. One has SQL Server and the other has a self-hosted integration runtime installed to connect to Azure.

1. Create two Windows VMs in Azure with the same virtual network/subnet
2. In VM1: Install SQL Server with sample database.
3. In VM2: Install Azure Data Studio and the self-hosted IR.
4. Test connection VM2 -> VM1.

***

## Create a resource group and network security group

See [Azure Resource Group and Network Security Group](../azure-rg-nsg/)

***

## Create a virtual network

See [Azure Virtual Network](../azure-virtual-network/)

***

## Create an "on-prem" SQL Server Windows VM

I will refer to it as the `SQL Server VM`. This will simulate the on-premise SQL Server.

Follow my tutorial [Create a SQL Server Windows VM](../azure-sql-server-vm/)

* Select the resource group and security group created above.
* In Networking: Select the Vnet created above `onprem-vnet`
* In SQL Connectivity: Select `Private (within Virtual Network)`
* RDP to the server, open SSMS and load a sample database.

***

## Create an "on-prem" Windows Server VM

I will refer to it as the `IR Server VM`. This will simulate another on-premise server. It is used for:

* Connecting to the SQL Server Windows VM in the virtual network.
* Installing the Self-Hosted Integration Runtime (IR) to connect to Azure Data Factory.

Follow my tutorial [Create an Azure Windows Server Datacenter VM](../azure-windows-server-datacenter-vm/)

* Select the resource group and security group created above.
* In Networking: Select the Vnet created above `onprem-vnet`

***

## Test Connection from IR Server VM to the SQL Server VM

Go to Azure to find the local IP address of the SQL Server VM `sql-server-IP`.

RDP to the IR Server VM. 

Open PowerShell and test the connection from IR Server VM to the SQL Server VM.

    Test-NetConnection sql-server-IP -port 1433

Output should be similar to this:

    ComputerName     : sql-server-IP
    RemoteAddress    : sql-server-IP
    RemotePort       : 1433
    InterfaceAlias   : Ethernet
    SourceAddress    : ir-server-IP
    TcpTestSucceeded : True

***

## Install Azure Data Studio and SSMS in IR Server VM

RDP to the IR Server VM.

Follow my tutorial [Use Chocolatey in Windows Powershell to Install Programs](../chocolatey-windows-powershell-install-programs/).

Then install these programs:

    choco install azure-data-studio --yes
    choco install sql-server-management-studio --yes

***

## Connect to SQL Server VM from the IR Server VM

Open SSMS in the IR Server and connect to the SQL Server VM

* In the connection
  * Enter the SQL Server local IP
  * Select Authentication Type `SQL Login`
  * Use the VM login credentials
  * When connecting accept the certificate
* Open `Databases` and `AdventureWorksDW2019` should be listed there.

Verify connection to the database by creating a query:

    SELECT TOP 20 * FROM DimCustomer;

**Disable the SQL Server VM RDP and Public IP**

Since this tutorial simulates that the SQL Server VM is an on-prem server with no public connection. Then RDP should be disabled.

* Go to Azure
* Find the SQL Server VM
* Go to Networking
* Select the RDP inbound port rule
* Click on the `...` and delete

***

## Create an Azure Data Factory

See:

* [Create an Azure Data Factory](../create-azure-data-factory/)
* [Build a pipeline with Azure Data Factory](../data-pipeline-azure-data-factory/)

Setup:

* Name the GitHub repo `onprem-adf`
* Name the data factory `onprem-adf`

***

## Create a self-hosted integration runtime (IR)

Once the ADF is deployed, open the resource. Then click on `Launch studio`

**Create an integration runtime**

* Inside the `Manage` hub
* Go to `Connections/Integration runtimes` and click on `New`
* Select `Azure, Self-Hosted`
* Select `Self-Hosted`
* Enter a name like `onprem`
* Enter a description like `Self-hosted IR from onprem Windows Server that connects to a private network onprem SQL Server`
* Click `Create`
* Under `Option 2: Manual setup`, open the link to `Download and install integration runtime`
* Copy/paste this link in the Windows Server to install the IR

**Setup the Integration Runtime in the IR Server VM**

For this tutorial, I started the two VMs:

* `onprem-ir` (IR Server VM)
* `onprem-sqlserver` (SQL Server VM).

Go to the IR Server and install the IR:

* Open the link from the ADF integration runtime setup
* Download the latest configuration (it was about 924MB)
* Run the installer and keep all defaults
* Then a window says `Register Integration Runtime`
* Go to the ADF setup for the IR and copy one of the Keys
* Then click `Register` and `Finish`
* A message should say `Integration runtime node has been registered successfully`

In ADF confirm that the self-hosted is running

* Go to Azure Data Factory
* Manage, Integration runtimes
* The created IR, in this tutorial I named it `onprem` should be listed and status is `Running`

***

## Connect onprem SQL Server to Azure Data Factory

Setup a Linked Service for onprem SQL Server. See [Azure Data Factory Linked Service](../azure-data-factory-linked-service/)


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)