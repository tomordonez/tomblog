---
layout: post
title: "TaskWarrior Task Management"
description: "Manage your tasks using the command line"
redirect_from:
  - /taskwarrior-task-management.html
categories: [Code]
image: assets/images/2.jpg
tags: [linux, taskwarrior, task management, productivity]
---

Manage your tasks using the command line.

I have been using TaskWarrior for a few few years.

Taskwarrior looks like this:

![TaskWarrior Task Management]({{ site.baseurl }}/assets/images/taskwarrior-tomordonez.jpg)

You can install Taskwarrior on Windows, Mac or Linux.

In Linux Fedora:

    $ sudo dnf install task

## Adding a task with TaskWarrior

Add a task:

    $ task add project:python Write awesome lesson learned due:today


## Updating a task

Update a task:

    $ task 1 modify due:monday


## List of tasks

Get a list of tasks:

    $ task list


## Completing a task

To complete a task:

    $ task 1 done


## Completed tasks:

Run this report:

    $ task completed


## Configuring a color theme in Taskwarrior

Open `~/.taskrc` and add this line:

    color=on

Then uncomment one of themes listed for example:

    include /usr/share/task/solarized-dark-256.theme

After saving and closing the file you can check which colors are used:

    $ task color legend

You can override colors back in `.taskrc` like this example:

    color.due=bold green
    color.alternate=

## Viewing Reports in Taskwarrior

Here are the default [Taskwarrior reports](https://taskwarrior.org/docs/report.html)

* `task active`: Show active tasks
* `task completed`: Show completed tasks
* `task next`: Show tasks ranked by urgency
* `task projects`: Show all projects used


## Creating Custom Reports in Taskwarrior

See report columns available. More [here](https://taskwarrior.org/docs/commands/columns.html)

Create a report called `simple` and edit Taskwarrior config located at `~/.taskrc`:

    dateformat=a D b Y
    report.simple.description=Simple list of open tasks by project
    report.simple.columns=id,project,description,due,start.age
    report.simple.labels=ID,Proj,Desc,Due,Active
    report.simple.sort=project+\/,due+
    report.simple.filter=status:pending

Then call the report with this command:

    $ task simple

## Configuring a bash script to clear the screen and list tasks

I saw that often I had to do this:

    $ clear
    $ task simple

I wanted to see the task list dashboard with just one word.

Create a `bin` directory in home folder and a bash file with those two commands:

    $ mkdir ~/bin/
    $ touch ~/bin/work
    $ chmod +x ~/bin/work

Inside the file add the location of bash (this is for Ubuntu)

    #!/bin/bash
    clear && task simple

Add this new directory to the path:

    $ export PATH="$HOME/bin:$PATH"

Then type the word `work` to clear the screen and show the list of tasks. Later I renamed the file from `work` to a letter to make it faster/simpler to type.

## Using annotation

Notes can be added to tasks. They are displayed below a task.

Add them like this example:

    $ task 6 annotate Review category csv file

Example report:

    ID Proj Desc                                   Due            
     1 db   App-Setup Controllers                  Mon 10 May 2021
     2 db   App-Setup Routes                       Mon 10 May 2021
     3 db   App-Setup Frontend                     Mon 10 May 2021
     4 db   App-Setup Load DemoData into test_data Mon 10 May 2021
     5 db   App-Setup Load DemoData Sale           Mon 10 May 2021
     6 db   App-Setup Load DemoData Category       Mon 10 May 2021
              Mon 10 May 2021 Review category
              csv file                 
     7 db   App-Setup Load DemoData Campaign       Mon 10 May 2021

## Undo a done task

Go to the completed report:

    $ task completed

Find the `UUID` of the task you want to bring back. This is the first column on that report. Let's say it's `da4bcc60`. Change the status to `pending`:

    $ task da4bcc60 modify status:pending

## Update many overdue tasks

Use this:

    $ task +OVERDUE mod due:today

## Workflow to manage tasks with Taskwarrior

Here is the workflow I use:

* Clear the screen and view the list of tasks `task simple` (or `work` using a bash script)
* Add tasks to a project with a due date `task add project:swe Complete assignment 1 due:today`
* Modify tasks if they are overdue `task 1 modify due:tomorrow`
* Start a task with `task start <id>` such as `task start 1`
* Annotate the task if I complete some of it such as `task annotate 1 Completed section 1A`
* Complete the task `task 1 done`
* Clear and view the list again `task simple`

## Troubleshooting: The expression could not be evaluated

I tried running this:

    task add project:idea Some awesome idea due:tomorrow

And the output was:

    The expression could not be evaluated

This was an issue on my version:

    task --version
    2.5.1

The issue was that it would not create projects that start with the letters `id`.

This was corrected by updating the version. In Linux Fedora the update was:

    sudo dnf update task
    Downloading Packages:
    task-2.5.3-1.fc34.x86_64.rpm

To review other issues that were solved look at Taskwarrior Github repo Changelog file [here](https://github.com/GothenburgBitFactory/taskwarrior)

Sources:

* [Taskwarrior color themes](https://taskwarrior.org/docs/themes.html)
* [Taskwarrior FAQ](https://taskwarrior.org/support/faq.html)
