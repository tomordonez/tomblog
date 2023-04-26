---
layout: post
title: "Power BI Publish to a Website"
author: tom
tags: [azure, power bi]
comments: true
---

How to publish a Power BI report to a website using Power BI Service.

This is a quick summary from experience, based on the MS docs [here](https://learn.microsoft.com/en-us/power-bi/collaborate-share/service-publish-to-web)

## Cannot publish to web from Power BI

If you don't see the option to publish a Power BI report to web. These might be the reasons:

* The feature is disabled or set to `Only allow existing codes` (embed code using iframe)
* You don't have a Power BI license
* The dataset is not in the same workspace as the report
* The report uses row-level security
* Other limitations in the MS docs [here](https://learn.microsoft.com/en-us/power-bi/collaborate-share/service-publish-to-web#considerations-and-limitations)

## Enabling Publish to Web in Power BI Service

This requires admin rights/access to `Admin Portal`

* In Power BI Service
* Click on the top right wheel settings
* One option should be `Admin Portal`

![Power BI Publish to a Website Admin Portal Menu](/assets/images/powerbi-publish-to-website-admin-portal-menu.png)

Under the `Tenant settings` menu:

* Scroll down to `Export and sharing settings`
* Then `Publish to web`
* Set to `Enable`
* Select `Allow existing and new codes`
* Click `Apply`

![Power BI Publish to a Website Enabled](/assets/images/powerbi-publish-to-website-enabled.png)

## The dataset is not in the same workspace as the report

In this example, Power BI won't let you export the `GitHub Pandas` report to the web. It opens the publish to web menu, however, when you try to `Publish`, it says `Creating code` but nothing happens.

![Power BI Publish to a Website No Dataset](/assets/images/powerbi-publish-to-website-no-dataset.png)

* Go to your workspace
* Check that the dataset is in the same workspace as the report

If the code generation works, the next screen should look like this:

![Power BI Publish to a Website Code Generated](/assets/images/powerbi-publish-to-website-code-generated.png)

## Embedding the code in a website

You can share the link that was generated and the link will open without having to login to Power BI.

You can also embed the `iframe` code in a web page...

<iframe title="Report Section" width="600" height="373.5" src="https://app.powerbi.com/view?r=eyJrIjoiZWY5YTI5ZjktMWIzMy00MTVmLWJkYjktYjY1YTAwZTc5ZTQ5IiwidCI6ImFlYWFhOWZmLTM2ZmYtNDJjOC1hYjE5LTI0OTgzMzUxYTY0MCJ9&pageName=ReportSection" frameborder="0" allowFullScreen="true"></iframe>

## Cannot find the embed codes

Go to the Power BI Service website. Go to the `Settings` wheel, then `Manage embed codes`

![Power BI Publish to a Website No Embed codes](/assets/images/powerbi-publish-to-website-no-embed-codes.png)

This UI is a bit weird. You would assume that it shows you all embed codes regardless of workspace. The data on this page loads based on your current workspace.

* On the left menu
* Click on the workspace where you shared the report
* On the top right menu. Click on the wheel `Settings` icon. Then `Manage embed codes`
* Now the codes should be listed.