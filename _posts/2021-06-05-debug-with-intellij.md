---
layout: post
title: "Debug with IntelliJ IDE"
description: "Debug with IntelliJ IDE"
author: tom
image: assets/images/1.jpg
tags: [intellij, java, java11]
---

Debug with IntelliJ IDE.

As seen in Java11 Essential Training in Linkedin Learning. Here is a process for debugging with IntelliJ.

Suspend the application at a point in the code. Inspect variables while suspended or evaluate expressions. Then leave debug mode.

## Debug with IntelliJ

Go to a line of code and click on the left side margin, next to the line number.

It will show a red dot.

![Image Name]({{ site.baseurl }}/assets/images/intellij-debug-red-dot.jpg)

Click on the bug icon. A new window at the bottom shows `debug`.

![Image Name]({{ site.baseurl }}/assets/images/intellij-debug-debugger.jpg)

The left pane shows where you are `main:13`. The right pane shows the variable values in scope

Click on the `step over` to run the next line.

![Image Name]({{ site.baseurl }}/assets/images/intellij-debug-step-over.jpg)

Click on `step out` to run out of the app. Remove the red dot by clicking on it.

## Debug Expressions

Select an expression to see the result

* Example. Select `i1 + i2`
* Right click
* Evaluate expression
* Evaluate