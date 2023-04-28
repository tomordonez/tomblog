---
layout: post
title: "Build a Pipeline with Azure Data Factory"
author: tom
tags: [azure, azure data factory]
comments: true
---

Building a pipeline with Azure Data Factory.

![Azure Data Factory Pipeline](/assets/images/azure-data-factory-pipeline.png)

**ETL process**

* Collect data sources
* Clean/join them with Dataflow and PowerQuery
* Create a master dataset
* Move the dataset to SQL Server

## Data Sources

Underrepresented and minority-owned companies found through Data.gov and the DOT website, also known as `DBE` or `Disadvantaged Business Enteprise`. There is a DBE website for every US state.

The US Department of Transportation has a website with all US states and their corresponding DBE websites [here](https://www.transportation.gov/osdbu/disadvantaged-business-enterprise/state-dot-and-disadvantaged-business-enterprise-web-pages). However, most of those links didn't work.

Using the google query `unified certification program`, leads you to most states DBE websites. Many of them use the same platform that lets you export the data.

This is a sample of one of the files with a sea of rows:

![Data Sources and Seas of Rows](/assets/images/data-source-sea-of-rows.png)

***

## Create an Azure Data factory

**Create a GitHub repo**

* Go to GitHub and create a repo.
* Set to private. Initialize with a Readme

**Create an Azure Data Factory**

* In the Azure portal
* Create a resource
* Search for `data factory`
* Select a `subscription` and create a `resource group`
* Enter a `name`, a `region` and keep the `version` as `V2`
* In `Git configuration` keep the default `Configure Git later`
* Go to `Review and Create` and `Create`

**Add the GitHub repo to Data Factory**

In Data Factory, add the repo:

* In the Manage interface, Source Control, `Git configuration`

![Azure Data Factory Git Repository](/assets/images/azure-data-factory-git-repository.png)

Select a GitHub repo:

* Enter the `GitHub repository owner` (your GitHub username)
* Authorize AzureDataFactory to access GitHub
* Select the repository you created
* Use the `Collaboration branch` as `main`
* Leave the default `Publish branch` as `adf_publish`
* Leave the default `Root folder`
* Check `Import existing resources to repository`
* Leave the default `Import resources into this branch` and select `main`
* Go to the GitHub repo and refresh to see updates

**Work in a branch**

In Data Factory, top left, a `main branch` will show up.

* On this drop down, click `New branch`
* Enter a branch name
* Do some work and click `Save all`. This will save the changes to the branch. And you can verify this in GitHub repo.
* Create a PR from the Data Factory or from GitHub

***

## Review the CSV files to build a schema

I did an initial processing of the data exports. They were in Excel and CSV with a lot of extra header/footer rows. A few didn't export correctly, and decided not to include them, as it would take me hours trying to clean them.

Many of them had these columns, while others had different column names, and more or fewer columns.

    "Company Name","DBA Name","Owner First","Owner Last","Physical Address","City","State","Zip","Mailing Address","City","State","Zip","Phone","Fax","Email","Website","Agency","Certification Type","Capability","County","Work Districts/Regions","SBE Certification"

**Schema columns**

I decided to use these columns. The insights could be to find the number of DBE companies by city or state. Or place them on a US map dashboard.

    CompanyName, DBAName, Address, City, State, Zip, Website, Agency, CertificationType, Capability, County

***

## Upload the data to the storage container

I created a container named `dbeapp` with two directories:

* `inputCSV`: I uploaded `12` CSV files
* `outputJoinedCSV`: I will use this after joining all files.

How to create a storage account with a container. Example in [Azure Analysis Services](../deploy-powerbi-model-azure-analysis-services/)

***

## Connect to the SQL Server Database

How to create an Azure SQL Server. Example in [PowerBI with Azure SQL Database](../powerbi-azure-sql-database/).

After the SQL Server and Database were created.

* Go to the database
* Query editor

Create a table to hold the data:

    CREATE TABLE Directory (
        CompanyID int PRIMARY KEY IDENTITY(1,1),
        CompanyName nvarchar(100) NOT NULL,
        DBAName nvarchar(100),
        Address nvarchar(100),
        City nvarchar(50),
        State nvarchar(20),
        Zip nvarchar(10),
        Website nvarchar(50), 
        Agency nvarchar(20), 
        CertificationType nvarchar(20), 
        Capability nvarchar(100),
        County nvarchar(50)
    )

However, much later in the process, I had problems with the data pipeline, and had to change the column lengths. More about this later.

In another blog post, I am going to build a star schema from this database since:

* `Directory` is not the best name for this table, change to `Company`
* Extract address information into a `CompanyAddress` table (since a company can have many address values)
* I think `Agency` and `CertificationType` might have a few dozen unique values and they can be in separate tables
* The database can have a fact table to make PowerBI easier to query than creating DAX calculations

Open the `Tables` to verify that the query ran correctly. I can see the columns in `dbo.Directory`

![Database in SQL Server](/assets/images/sql-server-database.png)

***

## Data Factory Components

We have to process the CSV files before combining them, since some files have different column names and number of columns.

**Create a Pipeline**

* Create a Pipeline for the project
 
**Create Linked Services**

These connect ADF to different services:

* For the Blob container
* For SQL Server

**Create Datasets**

A dataset is a view that points to the data at a service.

* 1 to join the output of the Data Flow
* 1 for PowerQuery output
* 1 for SQL Server

**Create a Data Flow**

* Create a Data Flow to process each CSV file

**Create a Copy Activity**

* A Copy Activity from PowerQuery output to SQL Server

There is a Youtube tutorial about some of the above workflow [here](https://www.youtube.com/watch?v=EpDkxTHAhOs)

***

## Create a Pipeline in Data Factory

* On the left menu, go to the Author/Edit interface.
* Under `Pipeline`, select `New Pipeline`
* Name: `Blob CSV to SQL`
* `Save all` to commit to the repository

![Azure Data Factory Pipeline](/assets/images/azure-data-factory-pipeline-resource.png)

***

## Create Linked Services

### Create a Linked Service for the Blob container

In Data Factory:

* On the left menu, go to the `Manage` interface
* Click on `Create linked service`
* Search for `Azure Blob Storage`
* Name `InputBlob`
* Integration runtime, leave the default `AutoResolveIntegrationRuntime`
* Authentication type `Account Key`
* Account selection method `From Azure subscription`, select your subscription and Storage account name.
* At the bottom click on `Test connection`
* Click `Create`
* `Save all` to commit to the repository

![Azure Data Factory Linked Services InputBlob](/assets/images/azure-data-factory-linked-services-inputblob.png)

### Create a Linked Service for SQL Server

* On the left menu, go to the `Manage` interface
* Click on `Create linked service`
* Search for `Azure SQL Database`
* Name: `OutputSQL`
* Connect using `From Azure subscription` and select your subscription, server name, and database.
* For `Authentication Type` select `SQL Authentication` (this has to be set when the server/db was created)
* Then `Test connection` and `Create`
* `Save all` to commit to the repository

***

## Create Datasets

### Create a Dataset to join the output of the Data Flow

We need to transform the CSV files from the Azure Blob, clean the data, and join them to a dataset. The input files are in the container (and directory) `dbeapp/inputCSV/`. The output container is `dbeapp/outputJoinedCSV/`.

* Go back to the Author/Edit interface
* Under `Datasets` click on `Add Dataset`
* Select `Azure Blob Storage`
* Select format `Delimited Text`
* Enter a name for example `OutputDataflowCSV`
* Select a linked service. The one you created above `InputBlob`
* Enter the file path with a file name `dbeapp/outputJoinedCSV/dbe-joined.csv`
* Select `First row as header`
* Import schema `From connection`
* `Save all` to commit to the repository

![Azure Data Factory Datasets](/assets/images/azure-data-factory-datasets.png)

### Using an input Dataset for PowerQuery processing

We will use this file `dbeapp/outputJoinedCSV/dbe-joined.csv` as the input dataset for PowerQuery.

### Create a dataset for the PowerQuery result

* In Data Factory, Author interface, click on Datasets
* Create new dataset, Azure Blob Storage, DelimitedText
* Rename to `OutputPowerQueryCSV`
* Select `Linked Service` as `InputBlob`
* Enter the file path with a file name `dbeapp/outputJoinedCSV/outputPowerQuery.csv`
* Check `First row as header`
* Import schema `From connection`
* `Save all` to commit to the repository

### Create a Dataset for the output SQL Server

* Go to the Author/Edit interface
* Create a dataset
* Select `Azure SQL Database`
* Enter a name. For my example `OutputSQLTable`
* Select the linked service `OutputSQL`
* In `Table`, select the table that was created above, in my case `dbo.Directory`
* Import schema, leave default `From connection/store`
* `Save all` to commit to the repository

***

## Create a Data Flow to process each CSV file

There are two transformation features in Azure Data Factory. Which one to use depends on the data source.

* Power Query: It requires a dataset in Data Factory
* Data Flow: It can use a dataset in Data Factory or a CSV file from Azure Blob

Since all the files are CSVs in the Azure Blob container. We are using Data Flow.

Follow my post [Azure Data Factory Data Flow](../azure-data-factory-dataflow/) for a detailed tutorial on this process.

Final result of the Data Flow, zoomed in:

![Azure Data Factory Data flow Zoom in](/assets/images/azure-data-factory-data-flow-zoom1.png)

***

## Create a PowerQuery resource

**Modify the dataset with PowerQuery**

When creating the table in SQL Server, I assumed a limit of characters for each column. For example:

    State nvarchar(20),
    Zip nvarchar(10),
    Capability nvarchar(100),

* I assumed that `State` could have been entered with two characters or with the full name.
* With `Zip` maybe it was entered with 5 digits or with 9 digits plus the dash character.
* For `Capability` during initial data exploration, this was a description of the company, and the few rows I saw weren't that long.

**Why use PowerQuery now?**

Later when you execute the Copy Activity to SQL Server. It might not work and throw an error that the data is being truncated.

* We could modify the dataset to match the data type lengths and/or
* Clean the dataset and modify the data type lengths in SQL Server

![Azure Data Factory Power Query](/assets/images/azure-data-factory-power-query.png)

**Create a PowerQuery resource**

* In Data Factory, Author interface, Factory Resources
* Go to Power Query, create `New power query`
* Rename it, for example `CleanCSV`
* Select a dataset, `OutputDataflowCSV`
* PowerQuery opens as an iframe
* `Save all` to commit to the repository

**Process the data with PowerQuery**

* Many columns had white space. I transformed all the columns to `Trim` them
* Another column had too many characters and I decided to truncate it to `250` characters
  * Formula `Text.Start([Capability], 250)`
* I also removed duplicates by `CompanyName + Address + CertificationType`
  * There were repeated rows but had different `CertificationType`
* `Save all` to commit to the repository

**Correct the Table in SQL Server**

Go to the SQL Server Table, then Query Editor. Recreated the table to map the max lengths for each column.

    DROP TABLE IF EXISTS [dbo].[Directory];
    GO

    CREATE TABLE Directory (
        CompanyID int PRIMARY KEY IDENTITY(1,1),
        CompanyName nvarchar(100) NOT NULL,
        DBAName nvarchar(150),
        [Address] nvarchar(150),
        City nvarchar(50),
        [State] nvarchar(2),
        Zip nvarchar(5),
        Website nvarchar(150), 
        Agency nvarchar(20), 
        CertificationType nvarchar(20), 
        Capability nvarchar(260),
        County nvarchar(50)
    );
    GO

**Add PowerQuery to the Pipeline**

* In Data Factory, Author interface, click on the Pipeline
* In Activities/Power Query, drag/drop `Power Query` to the graph canvas
  * Add a name, I entered `CleanCSV`
  * `Settings` tab
    * Select the PowerQuery created previously
    * Run on Azure or IR, leave default `AutoResolveIntegrationRuntime`
    * Compute type, leave default `General purpose`
    * Core count, leave default `4 (+4 Driver cores)`
  * `Sink` tab
    * Select as `Sink` the dataset created above `OutputPowerQueryCSV`
    * On the same row, click on the `Settings` icon
    * `Settings` tab, file name option `Output to single file` and enter the file name `outputPowerQuery.csv`
    * `Optimize` tab, select `Single partition`

**Connect Data flow to Power Query**

* Click on the `Data Flow` component
* On the small right-arrow, drag/drop this arrow to the Power Query component
* This means on Data Flow completion, do the next step Power Query.
* `Save all` to commit to the repository

**Errors with PowerQuery**

* If PowerQuery shows that `Cannot connect to one of the data sources`
* Make a backup of the steps you did so far, by going to the last step
* Click on `Advanced Editor` and copy/paste the code somewhere safe
* Delete the PowerQuery resource
* Add it again
* Reload the steps

***

## Create a Copy Activity from PowerQuery output to SQL Server

* Go to the Author/Edit interface
* Go to the Pipeline
* Under `Move & Transform`
* Drag/drop `Copy data` to the canvas (on the right)
* Name: `CSVtoSQL`
* Go to the `Source` tab and select the `Source dataset`. Select the `OutputPowerQueryCSV` dataset
* Go to the `Sink` tab and select the `Sink dataset`. Select the `OutputSQLTable` dataset
  * In `Pre-copy script` enter `delete from your-table-name`
    * For example `delete from Directory` (my table was called `Directory`)
    * This is similar to saying `IF EXISTS(something)...DELETE FROM your-table-name`
* Go to the `Mapping` tab to verify the input columns map correctly to the output ones.
  * Click on `Import schemas`
* `Save all` to commit to the repository

**Connect the Power Query activity to the Copy Data**

* Click on the Power Query activity
* On the small right-arrow, drag/drop this arrow to the Copy Data component
* This means on Power Query completion, do the next step Copy Data.
* `Save all` to commit to the repository

**Error: Copy activity CSVtoSQL**

I initially didn't add PowerQuery to clean the data and got this error:

    Failure happened on 'Sink' side. ErrorCode=SqlOperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=A database operation failed. Please search error to get more details.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.InvalidOperationException,Message=The given value of type String from the data source cannot be converted to type nvarchar of the specified target column.,Source=System.Data,''Type=System.InvalidOperationException,Message=String or binary data would be truncated.,Source=System.Data,'

The error means that the output will truncate the input because the data types don't match. I found out that the lengths I defined in the output were too short. I added a PowerQuery activity as described previously.

***

## Verify the data copied from Input Blob To SQL Server

* Go to SQL Server database
* Query editor
* Run a query `SELECT COUNT(*) FROM your-database-name`
* Verify the results

![Azure Data Factory SQL Server](/assets/images/azure-data-factory-sql-server.png)

**Run again using a Trigger**

So far the pipeline ran in debugging mode. Create a trigger to keep a record of a run in the `Monitor` interface.

* Go to the Data Factory
* `Add trigger`, then `Trigger Now`.
* It will monitor this run in the Monitor interface.

See more about triggers in [Azure Data Factory Scheduling](../azure-data-factory-scheduling/).

**Publish**

Click `Publish` to save everything into the `adf_publish` branch. An internal branch used by Azure Data Factory.