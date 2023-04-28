---
layout: post
title: "Azure Data Factory Self-Hosted Integration Runtime"
author: tom
tags: [azure, azure data factory]
comments: true
published: false
---

Connect an on-prem SQL Server to Azure Data Factory using a self-hosted integration runtime (IR).

## Summary

This simulates an on-prem environment by creating two servers in the same network. One has SQL Server and the other has a self-hosted integration runtime installed to connect to Azure.

The on-prem IR server connects to the on-prem SQL server to query tables and copy them to Azure Synapse through Azure Data Factory. The tables are stored in Azure Blob storage using Parquet files. These files are copied to Azure SQL Database for data warehouse. Then Power BI connects to this database to create reporting.

1. Create two Windows VMs in Azure with the same virtual network/subnet
2. In VM1: Install SQL Server with sample database.
3. In VM2: Install Azure Data Studio and the self-hosted IR.
4. Test connection VM2 -> VM1.
5. Create an ELT pipeline in Azure Data Factory to move data from "on-prem" VM1 (SQL Server) to Azure SQL.
6. Create a PowerBI report

***

## Create a resource group

In Azure, search for `resource group`

* Click Create
* Enter a name like `onprem-azure-dw`
* Select a region
* Leave other defaults and Create

***

## Create a virtual network

In Azure, search for `virtual network`

* Click Create
* Select subscription and resource group `onprem-azure-dw`
* Enter a name, for example `onprem-vnet` and select a region
* Select or modify the default subnet
* Leave other defaults and create.

***

## Create a network security group

In Azure, search for `nsg`

* Click Create
* Select subscription and resource group `onprem-azure-dw`
* Enter a name, for example `onprem-nsg` and select a region
* Leave other defaults and create.

***

## Create a SQL Server Windows VM

**I will refer to it as the IR Server VM**

In Azure, search for `Azure SQL`.

* Click Create
* On `SQL virtual machines` select an image such as `Free SQL Server License: SQL Server 2022 Developer on Windows Server 2022`
* Click Create
* Select the resource group `onprem-azure-dw`
* Enter a name like `onprem-sqlserver`
* Choose the lowest size with 4GB like `Standard B2s -2 vcpus, 4GiB`
* Enter user/pwd
* Public inbound ports. Keep RDP.
  * This can be disabled after loading the sample database
* Licensing
  * Don't select `Would you like to use an existing license?` if you don't have one.
* On `Disks` tab select `Standard SSD`
* On `Networking` tab
  * select the virtual network `onprem-azure-dw-vnet`
  * subnet `default`
* On `Management` tab
  * Enable `Auto-shutdown` if you want to make sure to stop the VM every day.
* On `SQL Server Settings` tab
  * SQL Connectivity `Private (within Virtual Network)`
  * Port `1433`
  * SQL Auth set to `Enable`
    * The default is to login using the same VM credentials
* On `Storage configuration` tab
  * Leave default for an actual prod env
  * For this blog post I lowered the settings.
  * Change configuration
  * Data storage, `Disk type` change to a lower setting like `16 GiB`
  * Log storage, change to `share the data drive for log`
* The `SQL Server License` should be `No`
* Click `Review and create`
* Select `Create`

***

## Create another Windows Server VM

**I will refer to it as the SQL Server VM**

This VM is used for:

* Connecting to the SQL Server Windows VM in the virtual network.
* Installing the Self-Hosted Integration Runtime (IR) to connect to Azure Data Factory.

In Azure, search for `Virtual Machine`

* Click Create
* Select the resource group `onprem-azure-dw`
* Enter a name like `onprem-ir`
* For `Image` click `See all images` or select the latest `Windows Server 2022 Datacenter Azure Edition`
* Choose the lowest size with 4GB like `Standard B2s -2 vcpus, 4GiB`
* Enter user/pwd
* Public inbound ports. Keep RDP
* On `Disks` tab select `Standard SSD`
* On `Networking` tab
  * select the virtual network `onprem-azure-dw-vnet`
  * subnet `default`
* Click `Review and create`
* Select `Create`

After provisioned:

* Go to networking
* Select RDP
* Change `Source` to My IP Address (Or select a different option)

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

## Install Azure Data Studio in IR Server VM

RDP to the IR Server VM. Open Powershell and install Azure Data Studio using Chocolatey. This is the Windows version of Linux's `apt` or `dnf`.

**Set up $profile in PowerShell**

See the contents of `$profile`:

    Write-Host $profile

This only shows the variable's content but the file doesn't exist there:

    C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

Create the file as seen in the Microsoft docs [here](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-item?view=powershell-7.3):

    New-Item -Path $profile -ItemType "file" -Force

**Install Chocolatey**

Install Chocolatey as seen in the official docs [here](https://docs.chocolatey.org/en-us/choco/setup).

    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

Even though, the docs don't fully explain exactly what this does. Here is a short summary form ChatGPT:

    ...this command sets up the PowerShell environment to allow the execution of scripts without any restrictions, updates the security protocol used for connecting to remote servers, and then downloads and installs the Chocolatey package manager using a script from a specific URL.

More detail about every command:

* `Set-ExecutionPolicy Bypass`: Sets PowerShell execution policy to Bypass for the current process
* `-Scope Process`: Only apply to the current PowerShell session
* `-Force`: Supress any confirmation prompt
* `-bor`: Bitwise OR operator
* `3072`: Represents the TSL 1.2 security protocol
* `[System.Net.ServicePointManager]::SecurityProtocol -bor 3072`: Bitwise OR between the current security protocol and TSL 1.2 to allow connection to remote servers
* `iex`: Same as PowerShell `Invoke-Expression` to execute a command
* `New-Object System.Net.WebClient`: Creates a `WebClient` object
* `DownloadString`: A `WebClient` method to download a script from a string
* `https://community.chocolatey.org/install.ps1`: Script to install Chocolatey. You can open this link to review the code.

Reload profile:

    . $profile

Restart PowerShell.

**For reference: Output after installing choco**

    Forcing web requests to allow TLS v1.2 (Required for requests to Chocolatey.org)
    Getting latest version of the Chocolatey package for download.
    Not using proxy.
    Getting Chocolatey from https://community.chocolatey.org/api/v2/package/chocolatey/1.3.1.
    Downloading https://community.chocolatey.org/api/v2/package/chocolatey/1.3.1 to C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall\chocolatey.zip
    Not using proxy.
    Extracting C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall\chocolatey.zip to C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall
    Installing Chocolatey on the local machine
    Creating ChocolateyInstall as an environment variable (targeting 'Machine')
    Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'
    WARNING: It's very likely you will need to close and reopen your shell
    before you can use choco.
    Restricting write permissions to Administrators
    We are setting up the Chocolatey package repository.
    The packages themselves go to 'C:\ProgramData\chocolatey\lib'
    (i.e. C:\ProgramData\chocolatey\lib\yourPackageName).
    A shim file for the command line goes to 'C:\ProgramData\chocolatey\bin'
    and points to an executable in 'C:\ProgramData\chocolatey\lib\yourPackageName'.

    Creating Chocolatey folders if they do not already exist.

    WARNING: You can safely ignore errors related to missing log files when
    upgrading from a version of Chocolatey less than 0.9.9.
    'Batch file could not be found' is also safe to ignore.
    'The system cannot find the file specified' - also safe.
    chocolatey.nupkg file not installed in lib.
    Attempting to locate it from bootstrapper.
    PATH environment variable does not have C:\ProgramData\chocolatey\bin in it. Adding...
    Chocolatey (choco.exe) is now ready.
    You can call choco from anywhere, command line or powershell by typing choco.
    Run choco /? for a list of functions.
    You may need to shut down and restart powershell and/or consoles
    first prior to using choco.
    Ensuring Chocolatey commands are on the path
    Ensuring chocolatey.nupkg is in the lib folder

**WARNING: Not setting tab completion**

See the Choco official docs [here](https://docs.chocolatey.org/en-us/troubleshooting) for troubleshooting.

If you get the warning:

    WARNING: Not setting tab completion: Profile file does not exist at 'C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1'.

Make sure that a `$profile` was created. Then edit `Microsoft.PowerShell_profile.ps1` and add this code:

    # Import the Chocolatey Profile that contains the necessary code to enable
    # tab-completions to function for `choco`.
    # Be aware that if you are missing these lines from your profile, tab completion
    # for `choco` will not function.
    # See https://ch0.co/tab-completion for details.
    $ChocolateyProfile = "$env:ChocolateyInstall\helpers\chocolateyProfile.psm1"
    if (Test-Path($ChocolateyProfile)) {
    Import-Module "$ChocolateyProfile"
    }

**Install Azure Data Studio**

Use `--yes` to confirm all prompts including `--accept-license`:

    choco install azure-data-studio --yes

Output:

    Chocolatey v1.3.1
    Installing the following packages:
    azure-data-studio
    By installing, you accept licenses for the packages.
    Progress: Downloading azure-data-studio 1.43.0... 100%

    azure-data-studio v1.43.0 [Approved]
    azure-data-studio package files install completed. Performing other installation steps.
    Merge Tasks:
    !runCode
    Downloading azure-data-studio 64 bit
    from 'https://sqlopsbuilds.azureedge.net/stable/b790d700898b1095d83e62f0de14678a58222520/azuredatastudio-windows-setup-1.43.0.exe'
    Progress: 100% - Completed download of C:\Users\...\AppData\Local\Temp\2\chocolatey\azure-data-studio\1.43.0\azuredatastudio-windows-setup-1.43.0.exe (128.31 MB).
    Download of azuredatastudio-windows-setup-1.43.0.exe (128.31 MB) completed.
    Hashes match.
    Installing azure-data-studio...
    azure-data-studio has been installed.
    azure-data-studio can be automatically uninstalled.
    Environment Vars (like PATH) have changed. Close/reopen your shell to
    see the changes (or in powershell/cmd.exe just type `refreshenv`).
    The install of azure-data-studio was successful.
    Software installed to 'C:\Program Files\Azure Data Studio\'

    Chocolatey installed 1/1 packages.
    See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).

I still don't get why the name `Chocolatey`. It says it's a pun from NuGet. I would have picked the name `loco` as in `loco install`.

Optionally install SSMS:

    choco install sql-server-management-studio --yes

***

## Load a sample database in SQL Server VM

**Copying files between VMs doesn't work**

I couldn't figure out how to copy a file from the IR Server VM to the SQL Server VM using `Copy-Item` in PowerShell since this gives an error:

    Copy-Item -Path "C:\Users\...\Documents\sampledatabase.bk" -Destination "\\IP-Number\C$\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup"

Error:

    Copy-Item: The network path was not found
    CategoryInfo: NotSpecified: Copy-Item, IOException
    FullyQualifiedErrorId: System.IO.Exception

I think the error has to do with setting up the firewall on the SQL Server VM. There was a solution that recommended disabling the firewalls but I wasn't comfortable doing that.

**Download the sample database in SQL Server VM**

Instead of doing this. RDP to the SQL Server VM and download the sample database there.

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

**Connect to SQL Server VM from the IR Server VM**

Open SSMS (Azure Data Studio lacks so much functionality) in the IR Server and connect to the SQL Server VM

* In the connection
  * Enter the SQL Server local IP
  * Select Authentication Type `SQL Login`
  * Use the VM login credentials
  * When connecting accept the certificate
* Open `Databases` and `AdventureWorksDW2019` should be listed there.

Verify that the database was restored by creating some queries. Right click on this database and `New Query`

    SELECT TOP 20 * FROM DimCustomer;

**Disable the SQL Server VM RDP and Public IP**

Since this tutorial simulates that the SQL Server VM is an on-prem server with no public connection. Then RDP should be disabled.

* Go to Azure
* Find the SQL Server VM
* Go to Networking
* Select the RDP inbound port rule
* Click on the `...` and delete

***

## Setup an Azure SQL Database to use as the DW

The workflow is:

* IR Server VM to Azure Data Factory (with or without Synapse)
* ADF ELT pipeline to Azure SQL Database or Azure Data Lake (star schema)
* Azure SQL or ADL to Power BI

This workflow may have three variations for Power BI:

* Use Power BI with Synapse
* Use Power BI on an Azure VM
* Use Power BI on-prem/local Windows machine

See [PowerBI with Azure SQL Database](../powerbi-azure-sql-database/) for complete details on setting up an Azure SQL Database.

* In Azure search for your SQL server (or follow above blog post to create one)
* Click `Create database`
* Enter a database name like `onprem-to-azuredw`
* Elastic pool set to `No`
* Computer + Storage set to `Basic`
* Backup redundancy set to `Locally-redundant backup storage`
* Leave other defaults and Create

***

## Create an Azure Data Factory

See complete details in [Build a pipeline with Azure Data Factory](../data-pipeline-azure-data-factory/)

**Create a GitHub repo**

* Set to private
* Initialize with a Readme
* Name it `onprem-adf`

**Create a Data Factory**

* In Azure, search for `Data Factories`, then `Create`
* Select the resource group `onprem-azure-dw`
* Enter a name like `onprem-adf`
* Git configuration
  * Uncheck `Configure Git Later`
  * Repository type `GitHub`
  * GitHub account: Enter your GitHub username
  * Enter Repo name `onprem-adf`
  * Branch `main`
  * Default `Root folder`
* Networking
  * Self-hosted integration runtime inbound connectivity, set to `Private endpoint`
* Private endpoint connections
  * Click `Create a private endpoint`
  * Select subscription and resource group `onprem-azure-dw`
  * Enter a name like `onprem-ir-endpoint`
  * On `Networking`
    * Select the virtual network `onprem-vnet (onprem-azure-dw)`
    * Select subnet `default`
      * A message says `If you have a NSG enabled for this subnet, it will be disabled for private endpoints on this subnet only. Other resources on the subnet will still have NSG (network security group) enforcement.`
  * On `Private DNS integration`
    * It says `To connect privately with your private endpoint, you need a DNS record. We recommend a private DNS zone. You can also use your own DNS servers or create DNS records using the host files on your VMs`
    * Set to `Yes`
    * Private DNS Zone: Leave default `(New) privatelink.datafactory.azure.net`
    * Click OK, then select it with a checkbox
* Leave other defaults
* Click `Create`

***

## Create a self-hosted integration runtime (IR)

Once the ADF is deployed, open the resource. Then click on `Launch studio`

**Confirm GitHub repo details**

* Check that the Github repo was configured
* On the left menu go to the `Manage` icon
* A message might say `Set working branch` and it should say `main`
* Go to `Source control/Git configuration` and it should have the repo `onprem-to-azuregh`

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

Go to the IR Server.

*Side note about RDP to the VM*

* If it doesn't connect, go to the VM, then `Networking`
* Select the RDP inbound port rules
* Check that the `Source` is selecting your IP numbers where you are connecting from.
* For this tutorial I selected `My IP address`

Install the IR:

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

**Setup a Linked Service for SQL Server**

* Go to ADF, Manage, Linked Services, New
* Search for `SQL Server`
* Enter a name like `onprem_sqlserver`
* Connect via integration runtime, select `onprem` (this is the self-hosted IR)
* For `Server name`, enter the private IP of the SQL Server VM
* For `Database name` enter the sample database `AdventureWorksDW2019`
* Authenticate with `SQL Authentication` and enter your user and pwd
* Click `Test connection` and it should say `Connection successful`
* Click `Create`

**Setup a Linked Service for Azure SQL Database**

This is used as the data warehouse for the star schema, which will be the source for Power BI.

* Go to ADF, Manage, Linked Services, New
* Search for `Azure SQL Database`
* Name it `onprem_to_azuredw`
* Connect with integration runtime `AutoResolveIntegrationRuntime`
* Account `From Azure subscription`
  * Select subscription, server name, database name `onprem-to-azuredw` (which was created before)
  * Authentication: `SQL authentication`
  * Enter user/pwd for the Azure SQL Server.
* Click `Test connection` then `Create`

***

## Create an Azure Data Factory pipeline

**Pending doc: Star schema in ADF**

Merge the data

1. Azure SQL Database is the staging DB with the star schema
2. Create star schema in staging DB and truncate the tables
3. Insert data from source tables
4. Run stored procedure with MERGE statements to update staging

SCD Type 2

* For the slowly changing dimension Type 2
* Keep the previous historical row and add a new row
* Add boolean attribute to identify the current row (set to True or False if not current)
* Add two date attributes to identify when the row started and when it ended
* For example, an employee was promoted from `Associate` to `Manager`
  * Type 1 would be to overwrite the `title` attribute
  * Type 2 requires to add the value and update the 3 attributes: `currentTitle`, `startDate` `endDate`

Incremental Loading - Setup

* Given a setup of source tables (my case: onprem SQL Server)
  * create table Customer (columns with PK)
  * create table Machine (columns with PK)
  * insert into ...(load values)
* Staging tables, and target DW tables (my case: Azure SQL or ADL)
  * create schema `[stg]`
    * create table Customer (columns no PK)
    * create table Machine (columns no PK)
    * create table MachineCycle (columns with PK)
  * create schema `[prod]`
    * create table DimCustomer (columns with PK and Type 2 SCD)
    * create table DimMachine (columns with PK and Type 2 SCD)
    * create table FactMachineCycle (columns with PK and FK)
    * create table DimDate
* Create ADL directory with subdir hierarchy year/month/day
  * Load `machine_cycles.csv`

Incremental Loading - ADF

* Goal
  * Incremental loading of Customer and Machine from source to staging
  * Transform from staging to production (with Type 2 SCD)

Auto create sink tables

* Data ingestion: Copy activity from onprem SQL to Azure SQL with Sink/Table option: Auto create table
* Data update: After data ingestion. Modify pipeline for incremental updates. Or create a different pipeline for the updates.

https://learn.microsoft.com/en-us/azure/architecture/data-science-process/move-sql-azure-adf

- Copy from onprem SQL Server to Azure Blob Storage
- Copy from Azure Blob Storage to Azure SQL Database

Why copy first to Azure Blob and not directly from SQL Server to Azure SQL?

ChatGPT says:

* Copy first to Azure Blob for performance, since it supports large datasets
* Once the data is in Azure Blob, you can copy it to Azure SQL DB at a faster rate than directly from SQL Server
* Azure Blob can also be used as a staging area to transform the data

Parquet files are more appropriate for OLAP when OLTP are the source. Parquet size is about 80-90% less than CSV, therefore much cheaper for storage. However, Parquet is immutable by default (only way to rewrite the data is to rewrite the table). For incremental/merge from SQL server to Azure Storage, use Delta file format that supports update/delete and also supports merge. Delta lake extends Parquet with ACID transactions (storage for Databricks)

Copy many tables from onprem to Blob:

* Two pipelines
* P1: GetTableListAndTriggerCopyData
  * Lookup SQL DB to get table list
  * Feed to and execute P2
* P2: IterateAndCopySQLTables
  * For each table in the table list
  * Copy data from SQL Table to Sink