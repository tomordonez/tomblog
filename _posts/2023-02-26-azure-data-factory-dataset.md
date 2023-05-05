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
* Select format from these options
  * Avro, Binary, DelimitedText (CSV), Excel, JSON, ORC, Parquet, XML
* Enter a name
* Select a linked service.
* Enter the container and if applicable: directory and file name
* Select `First row as header`
* Import schema `From connection`
  * Unless there aren't any files there yet, set to `No`
* `Save all` to commit to the repository

## Dataset for Azure SQL Database

* Go to the Data Factory
* Go to the Author/Edit interface
* Create a dataset
* Select `Azure SQL Database`
* Enter a name
* Select the linked service
* In `Table`, select the table
  * If no table was created, it will say `Loading` and then `Failed`
* Import schema, leave default `From connection/store`
* `Save all` to commit to the repository

## Dataset for on-prem SQL Server

* Go to the Data Factory
* Go to the Author/Edit interface
* Create a dataset
* Select `SQL Server`
* Enter a name
* Select the linked service
* Connect via integration runtime
  * Select the Self-Hosted integration runtime
  * See [Azure Data Factory Self-Hosted Integration Runtime](../azure-adf-self-hosted-integration-runtime/)
* In `Table name`, select the table to copy to the Storage Blog
* Import schema, leave default `From connection/store`
* `Save all` to commit to the repository

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)