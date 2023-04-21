---
layout: post
title: "Create a GitHub Dashboard with PowerBI Service"
author: tom
tags: [azure, power bi, github]
comments: true
---

This requires PowerBI Pro.

* Go to your PBI service. Left Menu/Apps. Get Apps.
* Select Githb Repository Usage.

## Githb Repository Usage App

This is the description of the app:

"Visualize different types of repository usage: commits, active users, traffic and more."

* Click on "Get It Now".
* A popup opens to confirm your details.
* If you don't have PowerBI Pro, it will show a popup 'Upgrade to Power BI Pro'.
* Select Buy Now or Try Free (for 60 days).
* You might need to repeat the steps if nothing is loaded on the page.
* A popup should say "Install this Power BI app? (Github Repository Usage)".
* Click "Install"

On the `Apps` tab (left menu), the app is listed as `Github`. Click on the app.

By default it says "You are viewing this app with sample data." With a link to "Connect your data". It is unclear at this point what dataset or repo was used or if it's dummy data.

I would like to know:

* Who are the top 50 users by group (MS employees or external)? Then find out commit frequency by group.
* What are the common commit subjects? What are they committing about?
* Which day of the week do they commit the most by group?

## Explore the dashboard

It looks like the dashboard is focused on PR and Issues. However, it's still not clear which repo is this data from. The data I am interested in, is in the tile `Commits`. It shows `Total commits` vs `Month-Year`. I would like to at least see `Total Commits` vs `Day of the week`.

On the top right of the tile, click on the three dots and select `Go to report`.

Click on the visual `Commits`. It shows these `Filters` on the right: `Date`, `MonthYear`, `Total commits is greater than 0`. I want to change the X-axis to `Day of the week`.

On the top right menu, browse to `Personalize this visual`. On the popup `Personalize`, change the X-axis field `MonthYear` to `DayName`. The visual is updated but the X-axis only shows `Sunday` data. Why?

**Something is not right with Commits**

There isn't a filter enabled that has only `Sunday` selected by default. Change the value from `DayName` to `DayOfWeek`. I'd assume it showed the numbers `1` to `7` on the X-axis. However, it only shows the number `1`.

Change the field back to `DayName`.

Change the `Date` field (from X-axis) by clicking the three dots and selecting `Date Hierarchy`. Keep only the field `Date-Year`. The visual is updated, however, now it shows only `Sunday` for every year from 2015 to 2020.

Let's explore the dataset where this visual is based on. On the visual (top right, three dots), select `Export`. A popup says "Which data do you want to export?". Click on "Underlying data" (Export the raw data used to calculate the data in your visual). The data is exported as `xlsx`.

Opening the file shows that it has `2108` rows. The first column says `DayName` and all the rows under that column say `Sunday`. Uh?

## Exploring the dataset

Go to the PowerBI service. On the left menu under Workspaces there should be one for the Github app. There are 4 sections:

* Dashboards
* Reports
* Workbooks
* Datasets
* Dataflows

Go to `Datasets` and open the only one there `GitHub`.

* The tables are displayed on the right.
* Select a table (checkbox) to view the data
* Find the table `RepoDetails`
* The repo is the [PowerBI docs](https://github.com/MicrosoftDocs/powerbi-docs)

**What insights can we derive by looking at the dataset?**

There is not enough information because it only shows a limited number of rows.

For example table `RepoStats`. Looking at the Commits column, it looks like people are working or contributing to commits on Sundays, with the most commits at 11am and at 5pm.

There are some options to quickly explore this data:

* Create a paginated report
* Export the CSV file and view it in another editor (like VScode)
* Open the file in PBI Desktop
* Open the file in PBI Report Builder. [https://aka.ms/pbireportbuilder](https://aka.ms/pbireportbuilder)

**Create a paginated report**

More about paginated reports in the Microsoft docs [here](https://learn.microsoft.com/en-us/power-bi/paginated-reports/paginated-reports-report-builder-power-bi).

* Go to the app `Github/Datasets/Github`.
* Select the table `RepoStats`.
* Click on `Create a paginated report`.

It shows the editor view with filter and values. Keep only these values:

* Commits
* Day

It groups the data by `Day` and it sums the `Commits` for each day. Although, there isn't an option to order from high to low. The highest commits are:

    Commits     Day
    3278        Tuesday
    3079        Wednesday
    2656        Monday

The top menu shows "Editing" and this drop down only has two options "Editing" and "Viewing". Go to File/Save to save the report under the Github Workspace. Then open the report. On the same dropdown, change from "Viewing" to "Edit in report builder". This will ask to download updates/install `PowerBI Report Builder`. See official docs [here](https://learn.microsoft.com/en-us/power-bi/paginated-reports/report-builder-power-bi).

**Exploring a CSV in VScode**

Export the table as a CSV, from the previous paginated report. Or export it from the table.

* Go to the app `Github/Datasets/Github`.
* Select the table `RepoStats`.
* Click `Export` (to CSV)

Open the CSV file in `MySQL`

* This is my preferred method. It just takes a few more steps to create a table.
* Follow my [Setup MySQL with VScode](https://github.com/tomordonez/learning/blob/main/docs/setup-mysql-vscode.md)

I have a database called `downloaded_csv` where I load CSV files as tables. Create the database in the SQL editor, then right-click `Run query`, and refresh `localhost`:

    CREATE DATABASE downloaded_csv;

Use the command line to convert the CSV file directly into the database:

    csvsql --db mysql://user:password@localhost:3306/downloaded_csv --tables pbi_docs_repostats --insert pbi_docs_repostats.csv

In the SQL editor:

    USE downloaded_csv;

    SELECT SUM(RepoStats_Commits) AS Commits, DAYNAME(RepoStats_Day) AS Day_Name
    FROM pbi_docs_repostats
    WHERE RepoStats_Commits IS NOT NULL
    GROUP BY Day_Name
    ORDER BY Commits DESC;

This results in the same data as the paginated report but in descending order by Commits.

    Commits	Day_Name
    3278	Tuesday
    3079	Wednesday
    2656	Monday
    2583	Thursday
    1951	Friday
    565	  Sunday
    308	  Saturday

## Explore the report

What report can be modified to answer any of these questions?

1. Who are the top 50 users by group (MS employees or external)? Then find out commit frequency by group.
2. What are the common commit subjects? What are they committing about?

Go to PBI Service. To the app `Github/Reports/Github`.

**Q1: Who are the top 50 users by group**

In the `Top 100 Contributors` there is a visual that shows users ranked by commits. Looking at the dataset where this comes from, the `Users` table doesn't identify what users are MS employees or external contributors. 

**Q2: What are the common commit subjects?**

In `Contributor Commits` there is a visual with commits and usernames. However, looking at the `Commits` table, it doesn't have a commit subject.

The page `Pull Requests` has a `Label` visual with data similar to topics. The table `Pull Requests` has a `title` column but there isn't a visual to represent this data.

From this dataset page, click on `Create a report` then `Start from scratch`.

* From the `Data`, click on `Pull requests`.
* Choose the `Table` visual.
* Drag the `title` column to the `Columns` field.
* Drag the `title` column again. On this column drop down, select `Count`
* On the table visual, order descending by `Count of title`

The top 5 most used titles are:

    Count of Title      Title
    22                  fix typo
    20                  Update refresh-data.md
    20                  Update service-admin-portal.md
    20                  Update service-gateway-sso-kerberos.md
    19                  Resolve syncing conflicts from rep_sync_working_brach to live

However, it's not clear if this count belongs to only one user or many users. I assume the first title `fix typo` might be used by more than one user. However the last one with 19 PRs, it is unlikely that the exact same title was used by different users.

Change the visual to `Matrix` and drag the `user` column to the `Columns` field.

* `fix typo` was used by 5 users
* `Update refresh-data.md` was used by 1 user.
* `Update service-admin-portal.md` was used by 3 users.

## Connect your data

At the top of the Github dashboard, it says `Connect your data`.

Let's try the `Pandas` repo.

* Enter owner `pandas-dev`
* Enter repo `pandas`

The next screen says `Connect to Github`:

* ExtensionDataSourceKind: `github` (this field can't be changed)
* ExtensionDataSourcePath: `github` (also can't be changed)
* Authentication method: `OAuth2` (drop down is enabled, but this is the only option)
* Privacy level settings: `Private` (It can be changed with the edit pencil)

Click `Sign in and connect`. It opens GitHub's authentication: `Authorize GitHub for Power BI`. You cannot edit the permission settings. Accept and continue. (You will get an email from GitHub that `a third-party OAuth application has been added to your account`)

Nothing happens. It gets stuck in `you're viewing this app with sample data refresh is in progress`

Go to the Workspace `GitHub` and it shows that the dataset is being refreshed...about an hour later it said `Update the app to apply and share any changes to this workspace`. Click on the `Update` button (top right on the Workspace). Then `Update app`, a popup says something like changes to the app can take up to a day...Nah! I clicked on Cancel.

The Workspace says that both Dashboard and Dataset were updated (see the date/timestamp). Rename dashboard, report, and dataset to `GitHub Pandas Repo`.

Open the Dashboard. This is definitely the `Pandas` repo.

* 210 open PRs
* 9,433 open issues

Open the Reports. All the visuals were updated.

I would like to create the `Pull Requests` report from scratch.

## Create a report from scratch

Leave this report open for reference. Open the PBI home in another tab.

* Click `Create`
* Click `Pick a published dataset`
* The `Pandas` dataset should be there, click on it
* In the `Auto-create report` button drop down, select `Create a blank report`

**Change the background**

On the top-left `View` menu enable:

* Show smart guides
* Show gridlines
* Snap to grid

On the `Visualizations` menu:

* Page information
  * Change `Name` to `Pull Requests Metrics`
* Canvas settings
  * Change `Vertical alignment` to `Middle`.
* Canvas background
  * Change the `Color` to light grey. Click `More colors`. Hex `#E6E6E6`
  * Move the `Transparency` all the way to the left.
* Wallpaper
  * Change the `Color` to another light grey `#EAEAEA`
  * Move the `Transparency` all the way to the left.

Save the report. `File/Save`

* Name: `GitHub Pandas PRs`
* Destination: `My workspace`

**Create the title visual**

* Click on the top menu option `Text box`
* Enter the text `Pull Requests`
  * Select the text
  * Font `Segoe UI`
  * Size `18`
  * Color `#070F25`
* Enter a new line with text `Last 12 Months`
  * Select the text
  * Font `Segoe UI Light`
  * Size `14`
  * Color `#070F25`
* Go to `Format/Background`
  * Change the transparency to `50%`

**Create the metrics cards**

* This week
* This month
* Last 12 Months
* Open
* Closed this month

For the first card:

* Use the field `Pull requests/Total pull requests` for
  * This week, This month, Last 12 Months
* Use a filter on the card visual based on the title date
* In `Visualizations/Visual`
  * In `Callout value`
    * Font `Segoe UI`, size `27`
* In `Visualizations/General`
  * Enable `Title`
    * Enter the card title
    * Heading 3
    * Segoe UI Light, 14
    * Text color `#070F25`
    * Horizontal alignment `center`
  * In `Effects
    * Background/transparency set to `50%`
* Select the card and do `Ctrl C`, `Ctrl V` to create the rest of the cards

**Create the "last updated" card**

* Create a table visual
* Select the `RepoDetails` table
* Add the fields `Repository` and `last run date`
  * For `last run date`
    * After adding the field, select the dropdown
    * Change from `Date hierarchy` to `last run date`
    * Rename to `Updated`
* Change the format like the other cards (although it's faster to use a theme)

**Create the "Pull Requests by Month" Visual**

Select the `Stacked area chart`.

* To the `X-axis`
  * From `Date` table, drag the field `MonthYear`
* To the `Y-axis`
  * From `Pull Requests` table
  * Drag `Open pull requests`
  * Drag `Closed pull requests`
* Change the title to `Pull Requests by Month`
* Remove the X and Y labels
* Set the format to match other visuals

**Create a table for top contributors of PRs**

* Create a Table
* To `Columns`, drag the fields
  * `user`
  * `Total pull requests`
  * `Open pull requests`
  * `Closed pull requests`
* Set the filter `Total pull requests` to `is greater than 10`