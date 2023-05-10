---
layout: post
title: "Power Query Permission Required to Run Native Database Query"
author: tom
tags: [power bi, power query, sql server]
comments: true
---

In Power BI getting data from SQL Server using Windows authentication and opening Power Query shows `Permission is required to run this native database query`.

As seen in ChatGPT, the Power BI forum and StackOverflow.

Most answers in the Power BI forum point to the Settings in Security to disable `Require user approval for new native database queries`. However, I don't think this is a good answer given that if the query has an alter table, it would modify the source table and break something else up/down stream.

A more correct solution is to review the security settings at the source in SQL Server and grant the proper permissions.

## With Windows Authentication to SQL Server

1. In SSMS connect to the SQL Server
2. Go to `Security` folder in Object Explorer. Right click on the Logins folder and select a New Login
3. For the corresponding user, in the `Login New` enter the `Windows account name`
4. Under `Server Roles`, select Public role
5. Under `User Mapping`, select the database, then select `db_datareader` and `public` roles
6. OK to save the login. 

## With Active Directory to SQL Server

1. SSMS connect to SQL Server
2. `Security` folder in `Object Explorer`. Right click on `Logins`. Select `New Login`
3. In `Login - New`, select `Windows Authentication`. Enter the `AD account name`
4. Under `User Mapping`, select the database, then grant `db_datareader` to the AD account.
5. OK to save the login.

## With Active Directory to Azure SQL Database

1. Check the Azure AD user account was granted permission to the SQL database as `db_datareader`
2. With Azure AD authentication
   1. Create an AAD user account or group
   2. Configure the SQL DB to use that account/group
   3. Use Power BI with `AAD with MFA` authentication

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)
