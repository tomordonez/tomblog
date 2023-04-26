---
layout: post
title: "PowerBI with Azure SQL Database"
author: tom
tags: [azure, power bi, Azure SQL Database]
comments: true
---

Connect PowerBI to Azure SQL Database.

Optionally, peform exploratory data analysis with Azure Data Studio to query the Azure SQL Database. Then use PowerBI to query and create the reports.

## Summary

1. Review your Azure Active Directory
2. Create an Azure SQL Server
3. Create an Azure SQL Database (optional, load a sample database)
4. Connect Power BI to the Azure SQL Database

## 1. Review your Azure Active Directory

This isn't such a problem if you are in a corporate account and you have admin rights to AD. However, it can be confusing if you have a personal Azure account or you aren't part of a custom domain.

According to the MS docs [here](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain). When you create an Azure account, it comes with Azure Active Directory (Azure AD) tenant with an initial domain name in the format `youremail.onmicrosoft.com`, such as if your email was `homer@gmail.com` then your domain would be `homergmail.onmicrosoft.com`, or `bart@outlook.com` then it would be `bartoutlook.onmicrosoft.com`.

You can verify this domain at the top right of your Azure account, where it shows your profile icon. Click on that profile icon and select `Switch directory`. The screen changes to `Portal settings | Directories + subscriptions`. The domain should be listed there.

When adding a custom domain, as noted in the docs above, such as `thesimpsons.com`, then you can add users to your AD that belong to that domain like `homer@thesimpsons.com`, `bart@thesimpsons.com`.

Without a custom domain, there is a mismatch between your Azure account and your Azure AD. If you open the Azure account with `bart@outlook.com`, the email domain `outlook.com` doesn't match the Azure AD domain `bartoutlook.onmicrosoft.com`.

**Default Active Directory**

In Azure, search for `azure active directory`.

This resource also shows the `Primary domain` like `bartoutlook.onmicrosoft.com`.

Click on `Users`. It will show your name (if you created the Azure account).

`Assigned roles` will show your user as part of `Global Administrator`. However, there is no indication that your user is related to your Azure accoung email. In other words, `bart@outlook.com` doesn't appear anywhere as being the email for the Azure AD admin user.

Why is this a problem?

When creating a resource in Azure that requires Azure AD authentication and setting an AD admin. You cannot select yourself as the admin. I found out the main reason is that you didn't login to Azure using an AD user. It's a chicken and egg problem. Create an Azure account (but you don't have an AD user), add yourself as an AD user but you didn't login with an AD account.

**Microsoft Account members are not allowed**

When creating an Azure SQL Server and trying to set the AD admin as noted above, you are shown an interface to select someone from a list of AD users. This will show your Azure email account but you cannot select it as it says `Microsoft Account members are not allowed`, regardless if your email is `outlook.com` or `gmail.com` or any other email that it's not part of your Azure AD.

**Add your Azure account to an Azure AD Group**

* In Azure, search for `Azure Active Directory`
* Click `Groups`
* Click `New group`
* Group type `Security`
* Enter a `Group name`
* Under `Members` select `No members selected`
* Search for your name (your Azure email account)
* Select this user and `Create`
* Click on the `Refresh` icon to confirm this was created

## 2. Create an Azure SQL Server

* Go to Azure and search `SQL servers`
* Click `Create`
* Select/create the `Resource group`
* Enter a `server name`
* Select the same location as the resource group
* Authentication method, select `Use both SQL and Azure AD authentication`
* Set an Azure AD admin by searching the AD group you created above.
* Create a server admin login/pwd
* Networking
  * Firewall rules
  * `Allow Azure services and resources to access this server`
  * Set to `Yes`
* Additional Settings
  * Enable MS Defender for SQL set to `Not now`
  * Enabling this will cost `$15/server/month`

**SQL Server set up Firewall**

* Go to the SQL Server
* Security
* Networking

External client:

* You can set a firewall rule to allow connecting from your external client
* `Public network access` should be in `Selected networks`
* Then create a firewall rule with your IP number (start and end can be the same)

Azure client:

* If your client is in your Azure account, for example an Azure VM
  * `Allow Azure services and resources to access this server` should be selected.

## 3. Create an Azure SQL Database (with sample DB)

We will load the sample database `AdventureWorksLT`.

Once the SQL Server is deployed

* Open this resource
* In `Overview`, click on `Create database`
* Subscription, resource group, and server are grayed out, since these are already pre-selected.
* Enter a `Database name` like `AdventureWorksLT`
* In `Want to use SQL elastic pool`, leave default `No`
* In `Computer + Storage`
  * Default selected was `General Purpose vCore` ($372.97 estimated cost/month)
  * Select `Configure`
    * Change `Service Tier` to `Basic` (5 DTUs, 2GB storage, $4.90/mo)
* Backup storage redundancy
  * Select `Locally-redundant backup storage`
* Networking
  * Leave default
* Security
  * Leave default, unless you want to sign up for MS Defender for SQL
* Additional Settings
  * In `Use existing data`, choose `Sample`.
  * It says `AdventureWorksLT will be created as the sample database.`
* Review + Create

**Cannot stop SQL server**

There isn't an option to stop the SQL server/database, like there is one for a VM. You will pay usage for the whole month.

## 4. Connect Power BI to the Azure SQL Database

**PowerBI is in an Azure VM**

As mentioned above, this has to be set in the Azure SQL Server:

* Security/Networking
* Below Firewall rules
* Under `Exceptions`
  * Select `Allow Azure services and resources to access this server`

**PowerBI Get Data from Azure SQL Database**

Go to PowerBI, get data, select Azure, then Azure SQL Database.

* Enter the server name
  * This is the full name from Azure Portal/SQL Server under `Server name`
* Enter the database name
* Data connectivity, select DirectQuery
  
Sign in:

* Select `Microsoft Account` and sign in to your Azure account
* Or sign in with SQL authentication

Power Query/Navigator opens:

* Select table(s)
* Select `Transform`
* Save, enter a file name
* If asked to transform, select `Apply Later`

**Azure Data Studio connect to Azure SQL Database**

* Create a connection to SQL Server.
* Enter server name.
* Select Active Directory authentication.
* Enter an account. Create new.
* It opens a link to login with your AD account.