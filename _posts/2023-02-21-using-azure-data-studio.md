---
layout: post
title: "Using Azure Data Studio"
author: tom
tags: [azure, azure data studio]
comments: true
---

Connect to Azure from Azure Data Studio. Generate an ER diagram.

Source:

* Download Azure Data Studio [here](https://learn.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio)
* What is Azure Data Studio? [here](https://learn.microsoft.com/en-us/sql/azure-data-studio/what-is-azure-data-studio)
* Quickstart docs [here](https://learn.microsoft.com/en-us/sql/azure-data-studio/quickstart-sql-server)
* Writing T-SQL docs [here](https://learn.microsoft.com/en-us/sql/t-sql/tutorial-writing-transact-sql-statements)

## Install Azure Data Studio in Ubuntu

Download the `deb` file.

Go to home and install the file (change to your exact file name)

    cd ~
    sudo dpkg -i ./Downloads/azuredatastudio-linux-1.40.2.deb
    
Output:

    Selecting previously unselected package azuredatastudio.
    (Reading database ... 202924 files and directories currently installed.)
    Preparing to unpack .../azuredatastudio-linux-1.40.2.deb ...
    Unpacking azuredatastudio (1.40.2-1671747874) ...
    Setting up azuredatastudio (1.40.2-1671747874) ...
    Processing triggers for mailcap (3.70+nmu1ubuntu1) ...
    Processing triggers for gnome-menus (3.36.0-1ubuntu3) ...
    Processing triggers for desktop-file-utils (0.26-1ubuntu3) ...
    Processing triggers for shared-mime-info (2.1-2)

Go to `Activities` and find `Azure Data Studio`

## Connect to a server

Create a connection to SQL Server.

Enter server name.

Select Active Directory authentication.

Enter an account. Create new. It opens a link to login with your AD account.

## Naming convention for database, table, column, schema

As seen in [this](https://stackoverflow.com/a/7724/1434022) answer in StackOverflow. Follow the `AdventureWorks` database sample from Microsoft:

* Database naming: lower case, no spaces/dashes/underscores
* Tables, columns, schema: UpperCamelCase, singular
* Views: Use the prefix lower `v` followed by ViewName like `vGetAllCategories`
* Store Procedures: Use the prefix lower `usp` (user stored procedure) followed by a Name, like `uspLogError`
* Primary key: `TableName + ID` such as CustomerID
* Foreign Key: `TableName + ID` such as AccountID
* Data types:
  * `nvarchar` instead of `varchar`
  * `datetime`
* Specify `null` or `not null`
* T-SQL:
  * End all queries with semicolon
  * Separate queries in batches using `GO`
  * Use brackets for column [Name] if they are reserved keywords or use friendlier columns like ProductName
  * If data type `nvarchar` insert values with `N` prefix like `N'Thor'`
  * Use keyword `INTO` in `INSERT INTO` even though Microsoft docs show with and without.

## Generate ER diagram in Azure Data Studio

In `Extensions` search for `Schema Visualization`. Follow the steps to install.

Clicking on `Install` opens the Github repo. Download the `VSIX` file. In Data Studio, in the Extension. Go to the 3-dots menu, click `Install from VSIX`, locate the file to install.

## Create a database in Azure

Create a database in the Azure portal:

* Enter all basic details
* Select `Compute+Storage` as `Basic`

## Create a schema in Azure Data Studio

In Azure Data Studio:

* Go to Connections
* Expand server/databases
* Right click on the database
* New query

Instead of using the default `dbo` schema, create your own. For example my schema is called `PL300`

    CREATE SCHEMA SchemaName;
    GO
    
## Create a table in Azure Data Studio

For example create the tables `Product` and `ProductSubcategory`

    CREATE TABLE PL300.Product
        (ProductID int PRIMARY KEY IDENTITY(1,1),
        ProductName nvarchar(25) NOT NULL);
    GO

    INSERT INTO PL300.Product (ProductName)
    VALUES
        (N'Bikes'),
        (N'Clothing');
    GO

    CREATE TABLE PL300.ProductSubcategory
        (ProductSubcategoryID int PRIMARY KEY IDENTITY(1,1),
        ProductSubcategoryName nvarchar(25) NOT NULL,
        ProductID int NOT NULL,
        FOREIGN KEY (ProductID) REFERENCES PL300.Product (ProductID));
    GO

    INSERT INTO PL300.ProductSubcategory
    VALUES
        (N'Mountain Bikes', 1),
        (N'Road Bikes', 1),
        (N'Gloves', 2);
    GO

## Refresh the database

Right click on the database, click `Refresh`

Expand the database, look for the table `PL300.Product`. Right click and `Select Top 1000`.

A query is created and the results are displayed.

## Visualize the schema

Right click on the database and select `Manage`.

On the left tab click on `Schema Visualization`. It should open something similar to an ERD (entity relationship diagram).

If there is a database connection error. Disconnect from the server. Close Data Studio. Open again and try.

## Create a View

Create a view to see all products with name and subcategory

    CREATE VIEW PL300.vProductAndProductSubcategory
        AS
        SELECT
            PL300.Product.ProductID AS ProductID
            , PL300.Product.ProductName AS ProductName
            , PL300.ProductSubcategory.ProductSubcategoryName AS ProductSubcategoryName

        FROM PL300.Product
        JOIN PL300.ProductSubcategory ON PL300.Product.ProductID = PL300.ProductSubcategory.ProductID;

    GO

Then query the view:

    SELECT * FROM PL300.vProductAndProductSubcategory;

## Create a stored procedure

Create a stored procedure to find products by category

    DROP PROCEDURE IF EXISTS PL300.uspGetProductByCategory;
    GO

    CREATE PROCEDURE PL300.uspGetProductByCategory @Category NVARCHAR(25)
        AS
        BEGIN
            SELECT * FROM PL300.vProductAndProductSubcategory
            WHERE ProductName LIKE @Category;
        END
    GO

Run with:

    EXECUTE PL300.uspGetProductByCategory 'Bikes';