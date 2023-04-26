---
layout: post
title: "Azure Data Factory Pricing"
author: tom
tags: [azure, azure data factory]
comments: true
---

These are estimates to calculate pricing for Azure Data Factory.

Sources:
* [Microsoft Azure Data Factory pricing](https://azure.microsoft.com/en-us/pricing/details/data-factory/data-pipeline/) * [Azure Data Factory pricing examples](https://learn.microsoft.com/en-us/azure/data-factory/pricing-concepts)
* [Azure Data Factory copy activity performance](https://learn.microsoft.com/en-us/azure/data-factory/copy-activity-performance)
* [Microsoft MVP Cathrine Wilhelmsen on Youtube about ADF pricing](https://www.youtube.com/watch?v=Fct1dcZMyWs)

**Scenario**

* An on-prem SQL Server
* An on-prem Windows Server with Self-Hosted Integration Runtime
* ADF copies tables from on-prem SQL Server to staging Azure SQL DB (through Self-Hosted IR Server)
* ADF transforms data from Azure SQL DB to production Azure SQL DB (star schema SCD Type 2)
* Production Azure SQL DB is used for Power BI

**Pricing assumptions**

* On-prem SQL Server has 100 tables.
* There is 1 copy activity from on-prem SQL Server to staging Azure SQL DB.
* There is 1 copy activity from staging Azure SQL DB to production Azure SQL DB.
* The default Copy setting uses `Auto` (4 DIU). You can change this setting to a lower number.
* There is only 1 pipeline in ADF.
* The ETL process takes 1 hour or less to run per day.

## Azure Data Factory: Monthly Pricing

**Orchestration**

Activity runs, trigger executions, debug runs. Runs round up to 1000 ($1 per 1000 runs)

- Azure IR: 30 runs = $1
- Self-Hosted IR: 30 runs = $1
- Total: $2

**Data movement activity**

2 copy activities for 100 tables. Assume the first time it takes 1-5min to copy each table but the next time is incremental. To make it simple to calculate, let's say it takes 30s for each table to copy. 100 tables * 30s = 50 minutes. Approximate to 1 hour to copy all 100 tables. However, seconds are rounded up to the minute. The 30s would round up to 1min. 100 tables * 1min = 100 minutes.

- Azure IR: $0.25/DIU-hour * 1.66hr * 4 DIU * 30 days = $50
- Self-Hosted IR (DIUs not applicable here): $0.10/hour * 1.66hr * 30 days = $5
- Total: $55

**Pipeline activity**

Lookup, Get Metadata, Delete, schema ops: test connection, browse folder list, table list, get schema, preview data. Activities are rounded up to the minute. You are also charged for inactive pipelines about `$1/mo`, delete them if you aren't using them.

- Azure IR: $0.005/hour * 30 days = $0.15
- Self-Hosted IR: $0.002/hour * 30 days = $0.06
- Total: $0.21

**External pipeline activity**

Executed on linked services. Databricks, storeproc, HDInsights.

- Azure IR: $0.00025/hour * 30 days = $0.0075
- Self-Hosted IR: $0.0001/hour * 30 days = $0.003
- Total: $0.011

**Dataflow Execution and Debugging**

.General purpose min cluster Dataflow 8 vcores. Assume it runs for 1 hour per day and it turns off.

- $0.274 per vCore-hour * 8 * 30 days = $65.76

**Data Factory Operations**

Read/Write: entities like datasets, linked services, pipelines, IR, triggers. Monitoring: get/list for pipeline, activity, trigger, debug runs. The docs say `records retrieved` but some non-MS blog posts say either `entities` or `runs`.

- Read/Write: $0.5 per 50,000 modified/referenced entities = $0.5
- Monitoring: $0.25 per 50,000 record retrieved = $0.25
- Total: $0.75

**Support**

- Standard: $100/mo (selected)
- Compared to Professional Direct: $1,000/mo

Total: PENDING

## Azure Data Factory: Development Pricing

**Assuming**

* The ETL process take 40 hours to develop
* Includes an onprem self-hosted IR and an Azure IR

**Orchestration**

- Assume it takes 30 runs
- Total: $1

**Data movement activity**

Assume that it took about 10 hours to develop this part to copy the tables, truncate them, copy them again, testing things, etc. 

Depending on the total size of the data. The MS docs recommend using a data sample of a size that takes at least `10 minutes` for the copy activity to complete. According to the table in `Copy Activity Performance` (see sources at the top of this page). A `10GB` dataset with `50Mbps bandwidth` will take around `30 min` to copy.

- Azure IR: $0.25/DIU-hour * 10hr * 4 DIU = $10
- Self-Hosted IR: $0.10/hour * 10hr = $1
- Total: $11

**Pipeline activity**

- Azure IR: $0.005/hour * 40 hrs = $0.2
- Self-Hosted IR: $0.002/hour * 40 hrs = $0.08
- Total: $0.21

**External pipeline activity**

- Azure IR: $0.00025/hour * 40 hrs = $0.01
- Self-Hosted IR: $0.0001/hour * 40 hrs = $0.004
- Total: $0.014

**Dataflow Execution and Debugging**

This assumes 8 work-hour/day and it is turned off during non-work hours.

- $0.274 per vCore-hour * 8 * 40 hours = $87.68

**Data Factory Operations**

- Read/Write: $0.5 per 50,000 modified/referenced entities = $0.5
- Monitoring: $0.25 per 50,000 record retrieved = $0.25
- Total: $0.75

Total: PENDING