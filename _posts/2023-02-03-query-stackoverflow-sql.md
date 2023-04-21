---
layout: post
title: "Query StackOverflow with SQL"
description: "Query StackOverflow with SQL and StackExchange Data Explorer"
author: tom
image: assets/images/1.jpg
tags: [analytics, sql, stackoverflow]
comments: true
---

Query StackOverflow with SQL and StackExchange Data Explorer.

Thanks to my friend Glenn, for the tip that StackExchange has a UI where you can query all SE sites like StackOverflow, Ask Ubuntu, and more (about 100 sites).

1. Create an account with StackExchange Data Explorer [here](https://data.stackexchange.com/) using OpenID.
2. Click on `Compose Query` to see the UI where to enter a query. It shows the database schema on the right.
3. Enter a name and description (optional) for the query
4. Compose the SQL query and click `Run Query`

## Database Schema

It's not very intuitive to understand the schema, even as a StackOverflow user. I assumed that all answers belonged to the Comments table.

* Database schema documentation [here](https://meta.stackexchange.com/questions/2677/database-schema-documentation-for-the-public-data-dump-and-sede)
* Schema ERD [here](https://i.stack.imgur.com/AyIkW.png)

This the structure of Posts:

* All questions and answers are in the Posts table
  * These are identified using `PostTypeId` (see table `PostTypes`)
* In StackOverflow, the comments that you see under questions or answers are in the Comments table

It's easier to understand the schema by looking at the ERD and querying individual tables.

**Find all PostTypes**

	SELECT * FROM PostTypes

Questions have a `PostTypeId` of `1` and answers are `2`.


**Find all Tags**

There are a lot of tags

	SELECT COUNT(Tags.Id) FROM Tags


**Find top 20 Tags with most count**

	SELECT TOP 20 Tags.Name FROM Tags ORDER BY Tags.Count DESC

The UI notifies if you made a SQL or schema mistake. The above query shows `Invalid column name 'Name'`. Correct to:

	SELECT TOP 20 Tags.TagName, Tags.Count FROM Tags ORDER BY Tags.Count DESC

These are the first 5 rows:

	javascript | 2470759
	python | 2097773
	java | 1885215
	c# | 1579042
	php | 1454705


**What are badges?**

	SELECT DISTINCT Badges.Name, COUNT(Badges.Name) AS Total FROM Badges GROUP BY Badges.Name ORDER BY Total DESC

If the queries start to get longer, then use your preferred best practice for indentation.

	SELECT
	  DISTINCT TOP 10 Badges.Name
	  , COUNT(Badges.Name) AS Total
	FROM
	  Badges
	GROUP BY
	  Badges.Name
	ORDER BY
	  Total DESC

These are the first 3 results:

	Popular Question | 6988567
	Notable Question | 3460033
	Editor | 3292748


**Find the top 10 users by most reputation**

	SELECT
	  TOP 10 Users.Id AS [User Link]
	  , Users.Reputation
	FROM
	  Users
	ORDER BY
	  Users.Reputation DESC

As seen in the documentation help [here](https://data.stackexchange.com/help), you can add special links called "magic columns" to access content on the site, for example, `[Post Link]`, `[User Link]`, `[Comment Link]`. Use the `Id` attribute of the corresponding table.

The top 3 results are:

	User | Reputation
	Jon Skeet | 1382399
	Gordon Linoff | 1224614
	VonC | 1185445


**Find users with the most down votes...sorry :(**

	SELECT TOP 10 Users.Id as [User Link], Users.DownVotes FROM Users ORDER BY Users.DownVotes DESC


**Find the top 10 users by most reputation and location**

	SELECT
	  TOP 10 Users.Id AS [User Link]
	  , Users.Reputation
	FROM
	  Users
	WHERE
	  Users.Location LIKE ##Location:string##
	ORDER BY
	  Users.Reputation DESC

	-- Location: Enter a city or country

This query uses SQL syntax specific to this UI as seen in the docs [here](https://data.stackexchange.com/tutorial/query-parameters). Enable user input with `##Variable##`, optionally add the data type `int`, `float`, or `string`. Create a comment with the variable name with more information (field label) about the variable. This comment can be added at the end of the query.

For Location enter `Miami`. These are top 3 results.

	User | Reputation
	Eduardo... | 13402
	tik27 | 2698
	codeSF | 1152

## Join Tables

**Find users with the most answers by location**

	SELECT
	  TOP 10 Users.Id AS [User Link]
	  , Users.AboutMe AS About
	  , COUNT(Posts.Id) AS Answer_Count
	FROM
	  Users
	  JOIN Posts ON Posts.OwnerUserId = Users.Id
	WHERE
	  Posts.PostTypeId = 2
	  AND Users.Location LIKE ##Location:string##
	GROUP BY
	  Users.Id
	ORDER BY
	  Answer_Count DESC
	
	-- Location: Enter city, state, or country


**Find users by location and tag**

Run my query directly in Data Explorer: [Find users by location and tag in StackOverflow](https://data.stackexchange.com/stackoverflow/query/1703843/find-users-by-location-and-tag)

## More queries

Go to my profile in [StackExchange Data Explorer](https://data.stackexchange.com/users/41128/tomordonez) to see the list of queries I made.