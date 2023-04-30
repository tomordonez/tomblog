---
layout: post
title: "Deploy a Power BI Model to Azure Analysis Services"
author: tom
tags: [azure, power bi, azure analysis services]
comments: true
---

Can we apply software engineering patterns like separation of concerns, single responsibility, and not repeating yourself to BI workflows?

I know a data analyst can do many functions from managing databases, connecting to data sources, creating data models, and design reporting/visualization. However, as you can see in job descriptions, they often look for a data analyst unicorn. Can one person do all these things? Probably they could, but they might not want to.

Narrowing down to separating data modeling from reporting design. This separation of responsibilities can be done with Azure Analysis Services.

## Separation of Concerns

Can we separate responsibilities? For instance, one person creates the data models, and another one designs the reporting and visualization? If the data modeling is done in Power BI, how do you share data modeling management with others? How do you standardize the data modeling process for management, security, and scale? Is there a way to decouple data modeling out of Power BI?

## Deploying a data model to Azure

Sources:

* [Why use Analysis Services](https://centida.com/analysis-services-vs-premium-power-bi/)
* [What is Azure Analysis Services](https://learn.microsoft.com/en-us/azure/analysis-services/analysis-services-overview)
* [Youtube tutorial about Azure Analysis Services](https://www.youtube.com/watch?v=4Fv6cHdL8S0)
* [Youtube tutorial installing Visual Studio and SQL Server Analysis Services](https://www.youtube.com/watch?v=Rx_mttQoZVg)
* [Youtube tutorial installing SQL Server Express](https://www.youtube.com/watch?v=LIytfaWH_10)

In this blog post I am going to describe how to:

1. Install Visual Studio in an Azure VM
2. Create an Azure Analysis Services server
3. Create a data model in Visual Studio
4. Deploy the model to Azure Analysis Services
5. Connect to the data model in Azure Analysis Services from Power BI.

## Why use Azure Analysis Services

There is a scenario where multiple users create reports using Power BI to find insights from the same data source, and create KPIs that were already previously designed by other users. Leading to repetitive tasks, basically doing the work all over again from scratch, without a proper standardization of data governance.

The disadvantages of this workflow are:

* Repetitive work among users
* KPIs are not standardized
* Data security is done in DAX during report design

Why use Azure Analysis Services:

* Adds a layer between the data source and Power BI, separating data modeling from reporting/visualization
* Move KPIs and data model out of Power BI. Move general KPIs out of Power BI. Users can define specific KPIs at reporting level in Power BI.
* Standardize KPIs in one shared tabular data model
* Data security is done in the Azure Analysis server

**What is Azure Analysis Services**

A managed platform for tabular data models in the cloud. Combine data sources, define metrics, and secure data. It doesn't support multidimensional models.

Based on SQL Server Analysis Services, it supports in-memory and DirectQuery:

* `In-memory`
  * Data is highly-compressed and cached
  * Fastest query response in large datasets
  * Data model can be partitioned for parallelization and reducing memory consumption.
  * Refresh must be programmed to update cached data from data sources
  * Uses Azure Blob storage to persist storage and metadata
* `DirectQuery`
  * Refresh programming is not required
  * It uses relational backend for storage and query execution
  * Uses Azure Blob storage only to store metadata

## StackOverflow Survey Dataset

Search for the current StackOverflow Survey dataset. Downloading the zip file contains:

* survey_results_public.csv (approx 100MB)
* survey_results_schema.csv (15KB)

## Install Visual Studio in an Azure VM

Follow this tutorial: [Use Chocolatey in Windows Powershell to Install Programs](../chocolatey-windows-powershell-install-programs/)

## Create an Azure Blob storage

Since we are using the `CSV` file from the Stackoverflow dataset. We have to upload it to an Azure Blob.

Follow my tutorial [Create an Azure Blob Storage and Container](../azure-blob-storage-container/)

Upload the CSV dataset `survey_results_public.csv` to the container.

**Preview the CSV file from PowerBI**

* Go to Power BI
* Get Data
* Azure
* Select `Azure Blob Storage`
* Enter the account name
  * From Azure, get the name of the Storage Account
* Enter the account key
  * From Azure, Storage Account, Access keys, copy `key1`
* Click `Connect`
* Selec the container, then Transform data.
* Click on `binary` content link.

## Create an Analysis Services server

1. Azure portal
2. Search for `Analysis Services`
3. Click on `Create`
4. Enter a server name, resource group, location
5. Pricing tier `D1 (20 Query Processing Units QPUs)` (used for dev or small models)
6. Click `Create`

Once it is deployed.

* You pay for usage. **PAUSE** when not using it.
* `Server name` is read only used for reporting
* `Management server name` is read/write for deploying models

## Load the dataset in Visual Studio

**Create an Analysis Services project**

1. Copy the `Management server name`
2. Open Visual Studio
3. Create a New project
4. Under `Analysis Services`, select `Analysis Services Tabular Project`, click Next
5. Enter Project name `StackoverflowModel`
6. For `Tabular Model Designer`
   1. Select `Integrated workspace`
   2. In `Compatibility level`, select the latest one.

**Add the data source**

1. In the window `Tabular Model Explorer`
2. Right click `Data sources`
3. Then `Import from data source` which opens PowerQuery.
4. Select `Azure` then `Azure Blob Storage`
5. Connect the same way as Power BI
6. Select the dataset, Transform Data, click on the Binary link.

**Transform the dataset**

1. Filter to keep some columns like technologies used, compensation, and a few more.
2. Go to column `DevType`
   1. Right click, then `Split`, Split by Delimeter. Then split using `semicolon`
   2. Then select all output columns `DevType`, right click `Unpivot columns`. Then remove the `Attribute` column and rename the `Value` column to `DevType`
3. Click `Import`
4. Save all.

On large datasets, create complex transformations at the data source, otherwise it might time out/crash from Analysis Services.

**Open Power Query Editor again**

It is not very intuitive to go back to the query editor screen. Once you click `Import` it opens the model in design mode.

On the right menu, go to the `Tables` folder:

* Right click on the table
* Then `Table Properties`
* Click the button `Design`
* It opens the Power Query Editor
* Continue doing other transformations as needed.
* Then click `Import` again.

Replace `NA` values with `0` before converting text to number. Otherwise the `Import` will fail after all the transformations were created and it doesn't save the steps.

To save the steps before importing.

* Go to Advanced Editor
* Copy the code and save to a text editor

**Process Table**

After importing and viewing the model as a table, it might show a lot of blank values that don't match the same data in Power Query Editor.

The tabular model must be updated.

There is a small icon at the top right called `Process Table`. Click there to refresh the table. Alternatively, go to the top menu `Extensions/Model/Process/Process Table`

## Setup the model deployment in Visual Studio

On the right window. In `Solution Explorer`:

* Right click on `StackoverflowModel`
* Select `Properties`
* Under `Deployment Server`
  * In `Server`, replace `localhost` with the Azure Analysis Services `Management Server Name`

**Deploy to Azure Analysis Services**

* Right click on `StackoverflowModel`
* Click `Deploy`
* It will ask to login to your Active Directory account
* Enter the `Storage Account` key
* A pop up window shows deployment progress
  * `Deploy metadata`: Success, Metadata deployed
  * `Datasets`: Success, 174,932 rows transferred

**Verify model deployed in Azure Analysis Services**

* Go to the Azure Portal.
* Go to the Azure Analysis Services resource
* In `Overview` the model `StackoverflowModel` should appear there

## Connect to Azure Analysis Services from Power BI

* On Azure Analysis Services
* Copy the `Server name`
* Open Power BI
* Get Data
* Select Azure, then `Analysis Services`
* Enter the server name
* Select `Connect live`
* Login using `Microsoft account`
* Then Navigator shows the `StackoverflowModel`. Select `Model`
* The table is loaded into Fields

## Define measures in Analysis Services

This is where we centralize KPIs, measures, and the data model, out of Power BI, into Azure Analysis Services.

Go back to Visual Studio.

Under the dataset table, there is an Excel-looking grid.

* Click on a cell to add a measure.
* Add the measure at the top where it says `fx`

Create measures using this syntax:

    MeasureName := DaxFunctionName(Parameters)

Given that the table is called `datasets`. Create this measure:

    JavaPercent:= FORMAT( (CALCULATE(COUNTROWS(datasets), datasets[Language] = "Java") / COUNTROWS(datasets)), "0.00%")

Once you hit enter it shows this result in the cell:

    JavaPercent: 6.47%

Create other measures for these:

    PythonPercent
    JSPercent
    SQLPercent
    CSharpPercent

**Deploy the model with the new measures**

* In Visual Studio, go to the right window, `Solution Explorer`
* Right click on the model `StackoverflowModel` then `Deploy`
* Login to your AD account

With all my dataset transformations and new measures:

    Deploy metadata.
    Datasets. (approx) 6,000,000 rows

**Refresh in PowerBI**

* Go to Power BI
* Click `Refresh` to see the latest model (from Azure Analysis Services)

## Install BISM Normalizer

As seen on the Youtube tutorial from "Azure for Everyone", they recommend to install an extension to review the changes in the model, sort of like a `git diff` to see changes between commits.

Go to `Extensions` and search for `BISM Normalizer`.

Then go to `Tools`, and `New Tabular Model Comparison`. Click on `Compare` and compare between your local model and Analysis Services.

## Visualize the measures in Power BI

Making changes to measures in Visual Studio and deploying again, just takes a few seconds.

Go to Power BI and refresh.

Create visualizations using the defined KPI measures and other fields.

## Stop the VM and Analysis Services

Shut down the Windows VM. Then go to Azure portal:

* Stop the VM
* Go to Azure Analysis Services and pause the server.