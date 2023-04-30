---
layout: post
title: "Copy a table from on-premise server to Azure Data Factory"
author: tom
tags: [azure, azure data factory, on-premise server]
comments: true
published: false
---

How to copy a table from an on-premise server to Azure Data Factory using a self-hosted integration runtime.

This tutorial simulates an on-prem environment by creating two servers in the same network. One has SQL Server and the other has a self-hosted integration runtime (IR) installed to connect to Azure.

The on-prem IR server connects to the on-prem SQL server to query tables and copy them to Azure Blob Storage through Azure Data Factory using Parquet files. These files are copied to Azure SQL Database for data warehouse. Then Power BI connects to this database to create reporting.

## Setup: Self-Hosted Integration Runtime

Follow my tutorial [Azure Data Factory Self-Hosted Integration Runtime](../azure-adf-self-hosted-integration-runtime/) that shows how to setup two servers that simulate an on-premise private network. You can follow that guide to setup the integration runtime in your actual on-premise network.

* Create a resource group, network security group, and virtual network
  * [Azure Resource Group and Network Security Group](../azure-rg-nsg/)
    * Resource group: `onprem-azure-dw`
    * Security group: `onprem-nsg`
  * [Azure Virtual Network](../azure-virtual-network/)
    * Vnet: `onprem-vnet`
* Create a Windows Server SQL Server VM connected to this Vnet
  * [Create a SQL Server Windows VM](../azure-sql-server-vm/)
    * Name `onprem-sqlserver`
* Create a Windows Server VM connected to the same Vnet
  * [Create an Azure Windows Server Datacenter VM](../azure-windows-server-datacenter-vm/)
    * Name `onprem-ir`
* Install the Self-Hosted Integration Runtime to the Windows Server VM
  * [Azure Data Factory Self-Hosted Integration Runtime](../azure-adf-self-hosted-integration-runtime/)
* Create a Data Factory and create a Self-Hosted Integration Runtime
  * Name `onprem-adf`
* Connect the Windows Server VM to this Integration Runtime

## Setup: Azure resources for this tutorial

Create these resources under the same subscription, resource group, and location:

**Resources**

* [Create an Azure Blob Storage and Container](../azure-blob-storage-container/)
  * 
* [Create an Azure SQL Database](../azure-sql-database/)

**Linked Services**

[Azure Data Factory Linked Services](../azure-data-factory-linked-service/) for:

* On-premise SQL Server
* Blob Storage
* Azure SQL Database

**Datasets**

[Azure Data Factory Datasets](../azure-data-factory-dataset/) for the corresponding Linked Services.

## Setup the Data Factory

* Pipeline for the Data Factory
* Copy Activity to connect from SQL Server to Blob
* Copy Activity to connect from Blob Azure SQL
* trigger to schedule the pipeline





[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)