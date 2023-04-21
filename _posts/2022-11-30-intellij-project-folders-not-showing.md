---
layout: post
title: "IntelliJ Project Folders Not Showing Up"
description: "After a git pull to main, IntelliJ folders were not showing up."
author: tom
image: assets/images/1.jpg
tags: [intellij, git, java]
comments: true
---

IntelliJ folders were not showing up after I git pull to main.

Previously I was in a branch where I added `.idea/` to `gitignore`. Pushed the
branch. Merged in Github. Then I checked out main and git pull. IntelliJ then
updated the changes but wouldn't show any project folders. It only showed the
`iml` project file.

As seen in stackoverflow
[here](https://stackoverflow.com/questions/5816419/intellij-does-not-show-project-folders).
The solution is to create the module again.

* File/Project Structure/Modules
* Add new module
* Create
