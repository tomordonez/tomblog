---
layout: post
title: "Azure Data Factory Linked Service"
author: tom
tags: [azure, azure data factory]
comments: true
---

Create a Linked Service in Azure Data Factory.

![Azure Data Factory Linked Services InputBlob](/assets/images/azure-data-factory-linked-services-inputblob.png)

## Create a Linked Service for Blob Storage

Go to the Azure Data Factory and launch the studio.

* On the left menu, go to the `Manage` interface
* Click on `Create linked service`
* Search for `Azure Blob Storage`
* Enter a name, for example: `InputBlob`
* Integration runtime, leave the default `AutoResolveIntegrationRuntime`
* Authentication type `Account Key`
* Account selection method `From Azure subscription`, select your subscription and Storage account name.
* At the bottom click on `Test connection`
* Click `Create`
* `Save all` to commit to the repository

## Create a Linked Service for Azure SQL Database

* On the left menu, go to the `Manage` interface
* Click on `Create linked service`
* Search for `Azure SQL Database`
* Enter a name, for example: `OutputSQL`
* Connect using `From Azure subscription` and select your subscription, server name, and database.
* For `Authentication Type` select `SQL Authentication` (this has to be set when the server/db was created)
* Then `Test connection` and `Create`
* `Save all` to commit to the repository

## Create a Linked Service for On-Premise SQL Server

* On the left menu, go to the `Manage` interface
* Click on `Create linked service`
* Search for `SQL Server`
* Enter a name like `onprem_sqlserver`

These settings are based on [Azure Data Factory Self-Hosted Integration Runtime](../azure-data-factory-linked-service/):

* Connect via the self-hosted integration runtime, select `onprem`
* For `Server name`, enter the private IP of the SQL Server VM
* For `Database name` enter the sample database `AdventureWorksDW2019`
* Authenticate with `SQL Authentication` and enter your user and pwd
* Click `Test connection` and it should say `Connection successful`
* Click `Create`

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)