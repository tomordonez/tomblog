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
2. Create an Azure SQL Database
3. Connect Power BI to the Azure SQL Database

## 1. Review your Azure Active Directory

This isn't such a problem if you are in a corporate account and you have admin rights to AD. However, it can be confusing if you have a personal Azure account or you aren't part of a custom domain.

When trying to set an Azure AD Admin in Azure SQL Server and looking for your account, you might get a `Microsoft Account Members Not Allowed`.

See [Azure AD Admin Microsoft Account Members Not Allowed](../azure-ad-admin-microsoft-account-members-not-allowed/) for troubleshooting.

## 2. Create an Azure SQL Database

Follow my tutorial [Create an Azure SQL Database](../azure-sql-database/) to:

* Create an Azure SQL Server
* Create a SQL Database

## 3. Connect Power BI to the Azure SQL Database

**PowerBI is in an Azure VM**

This has to be set in the Azure SQL Server:

* Security/Networking
* Under Firewall rules
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

Follow this tutorial to install Azure Data Studio or SSMS using Powershell. [Use Chocolatey in Windows Powershell to Install Programs](../chocolatey-windows-powershell-install-programs/)

* Create a connection to SQL Server.
* Enter server name.
* Select Active Directory authentication.
* Enter an account. Create new.
* It opens a link to login with your AD account.

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)