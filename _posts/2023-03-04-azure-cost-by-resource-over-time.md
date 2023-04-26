---
layout: post
title: "Azure Cost By Resource Over Time"
author: tom
tags: [azure]
comments: true
---

How to review the cost of Azure by resource over time.

![Azure Data Factory Cost Over time](/assets/images/azure-data-factory-cost-over-time.png)

Microsoft has detailed documentation about Azure cost analysis. This is how I quickly review the report of Azure for specific resources I used.

For example. How much it cost me in Azure to write this post about [Creating a data pipeline in Azure Data Factory](../data-pipeline-azure-data-factory/).

I used Azure Data Factory for 3 days, turning debug mode on and off. This creates a cluster and that's how Azure charges you for the service.

* Go to Azure portal
* Cost Management
* Cost Analysis

On the default dashboard:

* At the top menu
* Change the `View` to `DailyCosts`

![Azure Data Factory Cost](/assets/images/azure-data-factory-cost.png)

Then change the period. I selected the last 7 days. Then used the filter and selected the resource. In this case Azure Data Factory.

![Azure Data Factory Cost Bar Chart](/assets/images/azure-data-factory-cost-barchart.png)

