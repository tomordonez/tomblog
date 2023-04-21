---
layout: post
title: "Health Analytics with Azure Synapse, ADL, ADF, and Power BI"
author: tom
tags: [azure, azure data factory, synapse, azure data lake, power bi]
comments: true
published: false
---

**In progress**

These are the technical details for a health analytics dashboard with Azure Synapse.

## Architecture / Process

* Azure Function to download a file from the web
* Load the file to Azure Data Lake in Azure Synapse
* Create a data pipeline in Azure Data Factory
* Create a star schema into Azure Data Lake
* Load schema in Power BI
* Create DAX measures and reports in PBI
* Deploy report to Power BI Service

## Dataset

The dataset is from the public domain:

* 9GB data source with 8 million rows
* Removed personal identification and contact information

**Purpose**

Visualize on a US map the location of health providers by specialization.

