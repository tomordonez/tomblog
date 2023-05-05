---
layout: post
title: "Create an Azure SQL Database"
author: tom
tags: [azure, sql database]
comments: true
---

How to create an Azure SQL Database.

## Create an Azure SQL Server

* Go to Azure and search `SQL servers`
* Click `Create`
* Select/create the `Resource group`
* Enter a `server name`
* Select the same location as the resource group
* Authentication method, select `Use both SQL and Azure AD authentication`
* Set an Azure AD admin by searching the AD group you created above.
  * See [Azure AD Admin Microsoft Account Members Not Allowed](../azure-ad-admin-microsoft-account-members-not-allowed/) for troubleshooting.
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

## Create an Azure SQL Database

Optional: Load the sample database `AdventureWorksLT`.

Once the SQL Server is deployed. Select the size settings that best fits your needs. Here is an example:

* Open this resource
* In `Overview`, click on `Create database`
* Subscription, resource group, and server are grayed out, since these are already pre-selected.
* Enter a `Database name` like `AdventureWorksLT`
* In `Want to use SQL elastic pool`, leave default `No`
* In `Compute + Storage`
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

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)