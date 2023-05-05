---
layout: post
title: "DAX Studio and Power BI"
author: tom
tags: [azure, powerbi, dax studio]
comments: true
---

Using DAX Studio to query a Power BI model or develop DAX measures.

## Setup

* Open a Power BI report
* Download/install DAX Studio
* Open DAX Studio
* On the connection, select the Power BI report

## What is DAX Studio for?

* Develop DAX measures
* Create DAX queries to explore the Power BI model
* You cannot deploy the DAX measures from DAX Studio. You have to copy/paste the code to Power BI

**Why create DAX queries?**

Once you create a data model in Power BI. How do you query the model to explore the data?

**"Querying" the model with a Power BI visual**

For this experiment, I created a `factSales` table that has a column `SalesAmount` and a `dimCustomer` table where one of the columns is `Marital Status`.

I would like to query the data model so it returns the total sales by marital status.

In Power BI, you can do this:

* Create two DAX measures in the fact table to get total sales where the Marital Status is `M` and `S`
* Create a visual table adding these two measures
* Then remove the visual when you don't want this data anymore

## Use DAX Studio to create a measure

Use the keywords `DEFINE` and `MEASURE` with the syntax `TableName[YourMeasureName]`.

Use `EVALUATE` to output the result. However, this only works if the query result is a table. To return a scalar number, use the curly braces `{ TableName[YourMeasureName] }` to convert the scalar to a table:

    DEFINE
    MEASURE factSales[Sales Amount Married] =
    CALCULATE(
        SUM(factSales[SalesAmount]),
        DimCustomer[MaritalStatus] = "M"
    )

    EVALUATE
    { factSales[Sales Amount Married] }

This outputs the value `1400650`

**Output log doesn't point where the error is**

Something annoying about DAX Studio is that it often doesn't point exactly where an error is. For example in this code:

    DEFINE
    MEASURE factSales[Sales Amount Married] =
    CALCULATE(
        SUM(factSales[SalesAmount],
        DimCustomer[MaritalStatus] = "M"
    )

    EVALUATE
    { factSales[Sales Amount Married] }

The output error is `Query(8,1) The syntax for 'EVALUATE' is incorrect`. However, in line `8` and column `1` I have the keyword `EVALUATE` and nothing else. Where is the error? It doesn't explicitly point that the error is a missing parenthesis here `SUM(factSales[SalesAmount],`

**Deploying the measure to Power BI**

This is not how DAX Studio works. There isn't a `deploy measure` feature to automatically integrate this to Power BI. What you can do is develop the measure and test that it works. You don't need to create a visual in Power BI to preview the data. However, you still need to create a measure in Power BI and Copy/paste the code from DAX Studio.

    Sales Amount Married =
    CALCULATE(
        SUM(factSales[SalesAmount],
        DimCustomer[MaritalStatus] = "M"
    )

## Use DAX Studio to create a query

I want to query the Power BI model to see this data, where each column belongs to different tables. `Marital Status` comes from `DimCustomer` and `Sales Amount` comes from `factSales`.

    Marital Status      Sales Amount
        M                1500
        S                2000

As shown in the DAX Studio docs [here](https://daxstudio.org/docs/tutorials/writing-dax-queries/), use `SUMMARIZECOLUMNS` to generate a query using columns from different tables.

The syntax is:

    SUMMARIZECOLUMNS(
        GroupBy_ColumnName,
        FilterTable (optional),
        "ColumnName" (same as SQL keyword: AS "ColumnName")
        Expression
    )

Then the code is:

    EVALUATE
    SUMMARIZECOLUMNS(
        DimCustomer[MaritalStatus],
        "Sales Amount",
        SUM(factSales[SalesAmount])
    )
    ORDER BY
        "Purchase Amount" ASC

The result is a table:

    Marital Status      Sales Amount
        M                1400650
        S                1262550

## Use Query Builder in DAX Studio

This feature makes it easier to create a DAX query or DAX measure without having to know all the DAX coding syntax. It doesn't make sense to know all the DAX functions when you have a tool that creates this for you.

* Click on `Query Builder`
* Drag a column or measure to `Columns/Measure`
* (Optionally) drag another column to `Filters`
* Click `Update`
* This creates the DAX code that you can run or edit
* You can also click on `New` to `Add New Measure`

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)