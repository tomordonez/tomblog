---
layout: post
title: "Azure AD Admin Microsoft Account Members Not Allowed"
author: tom
tags: [azure, azure ad]
comments: true
---

When trying to set an Azure AD Admin in Azure SQL Server and looking for your account, you might get a `Microsoft Account Members Not Allowed`.

## Azure SQL Server: set AD Admin

If you are creating an Azure SQL Server, there is a setting to set `Azure AD admin`. See for reference [Create an Azure SQL Database](../azure-sql-database/).

This will open an overlay with the Active Directory where you can search your user name. However, if you didn't login to Azure with AD, your username might show the message `Microsoft Account Members Not Allowed` which it won't let you add your username as the AD Admin.

According to the MS docs [here](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain). When you create an Azure account, it comes with Azure Active Directory (Azure AD) tenant with an initial domain name in the format `youremail.onmicrosoft.com`, such as if your email was `homer@gmail.com` then your domain would be `homergmail.onmicrosoft.com`, or `bart@outlook.com` then it would be `bartoutlook.onmicrosoft.com`.

You can verify this domain at the top right of your Azure account, where it shows your profile icon. Click on that profile icon and select `Switch directory`. The screen changes to `Portal settings | Directories + subscriptions`. The domain should be listed there.

When adding a custom domain, as noted in the docs above, such as `thesimpsons.com`, then you can add users to your AD that belong to that domain like `homer@thesimpsons.com`, `bart@thesimpsons.com`.

Without a custom domain, there is a mismatch between your Azure account and your Azure AD. If you open the Azure account with `bart@outlook.com`, the email domain `outlook.com` doesn't match the Azure AD domain `bartoutlook.onmicrosoft.com`.

## Default Active Directory

In Azure, search for `azure active directory`.

This resource also shows the `Primary domain` like `bartoutlook.onmicrosoft.com`.

Click on `Users`. It will show your name (if you created the Azure account).

`Assigned roles` will show your user as part of `Global Administrator`. However, there is no indication that your user is related to your Azure accoung email. In other words, `bart@outlook.com` doesn't appear anywhere as being the email for the Azure AD admin user.

**Why is this a problem?**

When creating a resource in Azure that requires Azure AD authentication and setting an AD admin. You cannot select yourself as the admin. I found out the main reason is that you didn't login to Azure using an AD user. It's a chicken and egg problem. Create an Azure account (but you don't have an AD user), add yourself as an AD user but you didn't login with an AD account.

## Microsoft Account members are not allowed

When creating an Azure SQL Server and trying to set the AD admin as noted above, you are shown an interface to select someone from a list of AD users. This will show your Azure email account but you cannot select it as it says `Microsoft Account members are not allowed`, regardless if your email is `outlook.com` or `gmail.com` or any other email that it's not part of your Azure AD.

## Add your Azure account to an Azure AD Group

* In Azure, search for `Azure Active Directory`
* Click `Groups`
* Click `New group`
* Group type `Security`
* Enter a `Group name`
* Under `Members` select `No members selected`
* Search for your name (your Azure email account)
* Select this user and `Create`
* Click on the `Refresh` icon to confirm this was created


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)