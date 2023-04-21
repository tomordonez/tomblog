---
layout: post
title: "How to setup boards in JIRA"
description: "How to setup boards in a new JIRA project."
author: tom
image: assets/images/1.jpg
tags: [jira, scrum, kanban, product management]
comments: true
---

How to setup boards in a new JIRA project.

As seen on a Linkedin Learning video from Atlassian University 'Managing Jira Projects: 2 Managing Boards' and my setup.

## Create a Kanban board to track tasks

From the project, create a Kanban board, unless the project already had one by default. Use this board to track tasks.

## Create a Scrum board 'Story Board' to track sprints

Create a scrum board and name it `Story Board`.

Add a filter to show only issues of type: story and task. However, it requires a sprint.

* Create a sprint
* Add issues to the sprint
* Start the sprint
* Add a filter to show only stories and issues
    * Board settings
    * `General/Filter/Edit Filter Query`
    * Click on `Type` dropdown, select (Story, Task), `Save`
    * Go to the board to confirm the new setting

## Create a Kanban board 'Bug Board' to track bugs

Create a Kanban board and name it `Bug Board`.

* Add a filter to show only bugs
    * Board settings
    * `General/Filter/Edit Filter Query`
    * Click on `Type` dropdown, select Bugs, `Save`
    * Go to the board to confirm the new setting
* Add Backlog to Kanban board
    * `Settings/Columns`
    * There are two statuses in the `ToDo` column, if you want to separate them, similar to the scrum process of the product backlog, where status `backlog` should be in a backlog list, and `selected for development` are similar to `ready` for selection in a sprint planning.
    	* Drag `backlog` card from `ToDo` to Kanban backlog
    	* Leave `selected for development` in the `ToDo` column.	
    * Switch the `Epics` panel to `ON`
    	* It will display epics in a panel in the backlog, and not as cards on the board.

## Using swimlines to separate issues into epics

If a Kanban or Scrum board has issues from more than 1 epic

* Go to `settings/swimlanes`
* Set by `epic`
* The board separates the issues by epics

Or to divide streams of work

* By default Kanban boards include an 'Expedite' swimlane
* Used for items marked with Highest priority

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)