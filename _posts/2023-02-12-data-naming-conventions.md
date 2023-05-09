---
layout: post
title: "Data Naming Conventions"
author: tom
tags: [sql, database, conventions]
comments: true
---

Naming conventions for database, table, column, star schema, indexes, Azure, and PowerBI.

Following the `AdventureWorks` database sample from Microsoft:

**Database naming**

* `databasename`
* Lower case
* No spaces/dashes/underscores

**Tables, columns, schema**

* `TableName` or `ColumnName`
  * Use column [Name] if they are reserved keywords
  * Or use friendlier columns like `ProductName`
* UpperCamelCase
* No spaces
* Singular

**Views**

* Same as Tables and
* Use the prefix lower `v` followed by ViewName like `vGetAllCategories`

**Stored Procedures**

* Same as Tables and
* Use the prefix lower `usp` (user stored procedure) followed by a Name, like `uspLogError`

**Keys**

* Primary/Foregin key: `TableName + ID` such as CustomerID

**Indexes**

* Clustered: `PK_TableName_ColumnName`
* Non-Clustered: `IX_TableName_ColumnName`

## T-SQL Cheat Sheet (ChatGPT generated)

ChatGPT can be contradictory. The examples generated for tables used plural as in `Customers`. However, when I asked if I should use singular or plural when creating tables, it responded to use singular `Customer`.

**Most common DDL and DML**

    CREATE TABLE Customer (
        CustomerID INT IDENTITY(1, 1) PRIMARY KEY NOT NULL,
        FirstName VARCHAR(50),
        LastName VARCHAR(50),
        Email VARCHAR(100)
    );

    ALTER TABLE Customer
    ADD PhoneNumber VARCHAR(20);

    INSERT INTO Customer (CustomerID, FirstName, LastName, Email)
    VALUES (1, 'John', 'Doe', 'johndoe@example.com');

    UPDATE Customer
    SET Email = 'johndoe123@example.com'
    WHERE CustomerID = 1;

    DELETE FROM Customer
    WHERE CustomerID = 1;

**Clustered/Non-Clustered indexes**

ChatGPT's naming convention here was "whatever" :)

This will create a clustered index called "idx_CustomerID" for Customers

    CREATE CLUSTERED INDEX IX_Customer_CustomerID ON Customer (CustomerID);

This will create a non-clustered index called "idx_LastName" on the "LastName" column of the "Customers" table. Note that a table can have multiple non-clustered indexes.

    CREATE NONCLUSTERED INDEX IX_Customer_LastName ON Customer (LastName);

**MERGE**

Suppose you have two tables, "Sales" and "SalesStaging", that contain information about sales transactions. You want to update the "Sales" table with any new or updated transactions from the "SalesStaging" table. You can use the MERGE statement to perform this operation.

In this example, the MERGE statement merges the "SalesStaging" table into the "Sales" table based on the matching "SaleID" columns. If a matching row is found in both tables, the "SaleAmount" and "SaleDate" columns in the "Sales" table are updated to match the values in the "SalesStaging" table. If a matching row is not found in the "Sales" table, a new row is inserted with the values from the "SalesStaging" table.

    MERGE Sales AS target
    USING SalesStaging AS source
    ON target.SaleID = source.SaleID
    WHEN MATCHED THEN
        UPDATE SET
            target.SaleAmount = source.SaleAmount,
            target.SaleDate = source.SaleDate
    WHEN NOT MATCHED BY TARGET THEN
        INSERT (SaleID, SaleAmount, SaleDate)
        VALUES (source.SaleID, source.SaleAmount, source.SaleDate);

**UPSERT**

Suppose you have a table named "Customers" with the following columns: "CustomerID", "FirstName", "LastName", and "Email". You want to insert a new row into the table, or update an existing row if a row with the same "CustomerID" already exists.

Here's an example UPSERT statement using the MERGE statement:

In this example, the MERGE statement attempts to match the "CustomerID" value of the new row (1, 'John', 'Doe', 'johndoe@example.com') with an existing row in the "Customers" table. If a match is found, the existing row is updated with the new values for "FirstName", "LastName", and "Email". If no match is found, a new row is inserted with the values from the new row.

Note that in this example, the new row is specified using a VALUES clause inside the USING clause. In practice, you may use a subquery, a table-valued parameter, or another method to provide the new row values to the MERGE statement. Also note that the MERGE statement is available starting from SQL Server 2008.

    MERGE Customers AS target
    USING (VALUES (1, 'John', 'Doe', 'johndoe@example.com')) AS source (CustomerID, FirstName, LastName, Email)
    ON target.CustomerID = source.CustomerID
    WHEN MATCHED THEN
        UPDATE SET
            target.FirstName = source.FirstName,
            target.LastName = source.LastName,
            target.Email = source.Email
    WHEN NOT MATCHED BY TARGET THEN
        INSERT (CustomerID, FirstName, LastName, Email)
        VALUES (source.CustomerID, source.FirstName, source.LastName, source.Email);

**Identify duplicate rows**

Suppose you have a table named "Orders" with columns "OrderID", "CustomerID", and "OrderDate". You want to find all duplicate rows in the table based on the "OrderID" column.

In this query, the GROUP BY clause is used to group the rows in the "Orders" table by the "OrderID" column. The COUNT(*) function is used to count the number of rows in each group, and the HAVING clause is used to filter out any groups with only one row. The result set shows the "OrderID" values that appear more than once in the table, along with the number of duplicates for each value.

    SELECT OrderID, COUNT(*) AS NumDuplicates
    FROM Orders
    GROUP BY OrderID
    HAVING COUNT(*) > 1;

If you also want to see the actual rows that are duplicated, you can use a subquery like this:

In this query, the subquery in the WHERE clause identifies the "OrderID" values that appear more than once in the "Orders" table. The outer query then returns all rows from the "Orders" table that have one of those "OrderID" values. The result set shows all rows that are duplicated in the table.

    SELECT *
    FROM Orders
    WHERE OrderID IN (
        SELECT OrderID
        FROM Orders
        GROUP BY OrderID
        HAVING COUNT(*) > 1
    );

**Remove duplicates**

Suppose you have a table named "Orders" with columns "OrderID", "CustomerID", and "OrderDate". You want to remove any duplicate rows from the table based on the "OrderID" column.

In this query, a common table expression (CTE) is used to assign a row number to each row based on the "OrderID" column, with duplicates receiving the same row number. The PARTITION BY clause specifies the "OrderID" column to partition the result set into groups, and the ORDER BY clause ensures that the rows within each group are sorted in a consistent manner. The ROW_NUMBER() function is used to generate the row numbers.

The DELETE statement then removes any rows from the CTE where the row number is greater than 1, effectively deleting all but one instance of each duplicated "OrderID". The result is a table with no duplicate "OrderID" values.

    WITH CTE AS (
    SELECT OrderID,
            ROW_NUMBER() OVER (PARTITION BY OrderID ORDER BY OrderID) AS RowNum
    FROM Orders
    )
    DELETE FROM CTE WHERE RowNum > 1;

**UNPIVOT**

How to use the UNPIVOT operator in T-SQL to transform columns into rows:

Suppose you have a table named "Sales" with columns "Month", "ProductA", "ProductB", and "ProductC". The "ProductA", "ProductB", and "ProductC" columns contain the sales figures for each product in each month. You want to transform this table into a "normalized" form with three columns: "Month", "Product", and "Sales".

In this query, the UNPIVOT operator is used to transform the "ProductA", "ProductB", and "ProductC" columns into rows, with the sales figures in a new "Sales" column. The "FOR Product IN" clause specifies the names of the columns to unpivot, and the AS keyword is used to give the unpivoted result set an alias of "unpvt".

The result set shows the sales figures for each product in each month, with one row per product per month. The "Product" column identifies which product the sales figure belongs to.

    SELECT Month, Product, Sales
    FROM Sales
    UNPIVOT (
    Sales FOR Product IN (ProductA, ProductB, ProductC)
    ) AS unpvt;

## Other T-SQL tips

* `nvarchar` instead of `varchar`
  * insert values with `N` prefix like `N'Thor'`
* `datetime`
* Specify `null` or `not null`
* End all queries with semicolon
* Separate queries in batches using `GO`
* Use keyword `INTO` in `INSERT INTO` even though Microsoft docs show with and without.

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)