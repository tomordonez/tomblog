---
layout: post
title: "Azure Data Factory Dataset"
author: tom
tags: [azure, azure data factory]
comments: true
---

Create datasets in Azure Data Factory.

See:

* [Create an Azure Data Factory](../create-azure-data-factory/)
* [Azure Data Factory Linked Service](../azure-data-factory-linked-service/)

## Dataset for Azure Blob Storage

* Go to the Data Factory
* Go to Author/Edit interface
* Under `Datasets` click on `Add Dataset`
* Select `Azure Blob Storage`
* Select format `Delimited Text`
* Enter a name
* Select a linked service.
* Enter the file path with a file name
* Select `First row as header`
* Import schema `From connection`
* `Save all` to commit to the repository

## Dataset for Azure SQL Database

* Go to the Data Factory
* Go to the Author/Edit interface
* Create a dataset
* Select `Azure SQL Database`
* Enter a name
* Select the linked service
* In `Table`, select the table
* Import schema, leave default `From connection/store`
* `Save all` to commit to the repository


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)