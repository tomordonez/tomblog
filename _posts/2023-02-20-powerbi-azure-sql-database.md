---
layout: post
title: "PowerBI with Azure SQL Database"
author: tom
tags: [azure, power bi, Azure SQL Database]
comments: true
---

Connect PowerBI to Azure SQL Database.

Optionally, peform exploratory data analysis with Azure Data Studio to query the Azure SQL Database. Then use PowerBI to query and create the reports.

## Create the sample database

Create the sample database `AdventureWorksLT` as seen in the Microsoft docs [here](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

1. Go to the Azure portal
2. Create a resource
3. Under `SQL Database` select `Create`

**Basics**

Project details

* Select `Subscription`
* Select `Resource Group` or create one

Database details

* Enter a `Database name`
* Select a `Server`
  * If creating a new one:
    * Enter a `Server name`
    * Select a `Location` closest to you.
    * Select `Authentication Method`
      * If you are in an `AD`
        * Select `Use only Azure Active Directory (AD)`
          * Set `Azure AD admin`
          * Find your name in the `Azure Active Directory`
      * Else, select `Use SQL Authentication`
* In `Want to use SQL elastic pool`, leave default `No`
* In `Computer + Storage`
  * Default selected was `Standard S0` (10 DTUs database transaction units, 250GB Storage, $14.72/mo)
  * Select `Configure`
    * Change `Service Tier` to `Basic` (5 DTUs, 2GB storage, $4.90/mo)

Backup storage redundancy

* Select `Locally-redundant backup storage`

**Networking**

Leave the default:

* Connectivity method: `No access`
* Connection policy `Default`
* Minimum TLS version `TLS 1.2`

**Security**

Leave the default:

* Enable Microsoft Defender for SQL ($15/server/mo): Select `Not now`
* Leave other defaults

**Additional Settings**

In `Use existing data`, choose `Sample`.

An overlay says:

    AdventureWorksLT
    Selecting this sample will modify the Compute+Storage settings configured in Basics, for backup compatibility. Visits the Basics tab again to review.

Click `OK` to continue.

**Review + Create**

Review all details and click `Create`. Then wait a few seconds (or minutes) for deployment.

## Cannot stop SQL server

There isn't an option to stop the SQL server/database, like there is one for a VM. You will pay usage for the whole month.

## SQL Server set up Firewall

Go to the SQL Server:

* Security/Networking/Public Access
* Public network access
  * Set to `Selected networks`
  * Click `Save` (or the firewall rule will fail)
* Firewall rules
  * Click the plus on `Add your client IP address`
    * This IP is dynamic so it might change
    * Check local IP again if there are connection issues from client
  * Enter a rule name
* Save

**PowerBI is on an Azure VM**

As seen in my [Setup Power BI in an Azure VM](../setup-powerbi-in-azure-vm/). You need to give SQL Server access to the Azure VM

* Below Firewall rules
* Under `Exceptions`
  * Select `Allow Azure services and resources to access this server`


## PowerBI Get Data from Azure SQL Database

Go to PowerBI, get data, select Azure, then Azure SQL Database.

* Enter the server name
  * This is the full name from Azure Portal/SQL Server
* Enter the database name
* Data connectivity, select DirectQuery
  
Sign in:

* Select `Microsoft Account` and sign in to your AD account

Power Query/Navigator opens:

* Select table(s)
* Select `Transform`
* Save, enter a file name
* If asked to transform, select `Apply Later`

## Azure Data Studio connect to Azure SQL Database

* Create a connection to SQL Server.
* Enter server name.
* Select Active Directory authentication.
* Enter an account. Create new. It opens a link to login with your AD account.