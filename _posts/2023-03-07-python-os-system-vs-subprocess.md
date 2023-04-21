---
layout: post
title: "Python os.system vs subprocess.call"
description: "How to use Python subprocess to run OS commands"
author: tom
image: assets/images/1.jpg
tags: [python, subprocess]
comments: true
---

How to use Python subprocess to run OS commands.

I like using `os.system` because you can run your favorite Linux commands. The `os` module has built-in methods, but often they aren't mapped exactly to Linux's commands.

For example, show the current working in directory.

In Linux

	pwd

In Python

	import os
	os.getcwd()

Then usually it's easier to remember this:

	import os
	os.system("pwd")

I've seen that `os.system` doesn't wait to complete, similar to what you would expect from a callback function.

	for url in urls:
		os.system("curl some-resource")
	print("Hello")

Not exactly with this example, but I've seen the program continue execution before `os.system` is completed, which leads to errors if the program depends on the output of `os.system`.

## Replace os.system with subprocess.call

`subprocess.call` waits for the command to complete.

In `os.system`:

	import os
	os.system("curl some-resource")
	# it might continue executing before os.system is done

In `subprocess.call`

	import subprocess
	subprocess.call("curl some-resource", shell=True)
	# it waits for subprocess to complete

## Use sleep() to wait for the process to start

I have seen this problem as well:

	subprocess.call("do something here")
	run_this_function()

`run_this_function()` executes first and then `subprocess.call` starts.

It executes `run_this_function()` which uses a file produced by the subprocess, and it shows this error:

	some-file.csv not found

Then it runs the process that was supposed to produce this file.

Isn't `subprocess.call` supposed to wait until it's done to execute the next line? Sure. According to the Python docs, it waits to complete. But the docs don't say anything about how long does it take for a process to start. Or maybe it starts, returning zero, executes the next line, and then it completes.

Quick fix with `sleep()`:

	subprocess.call("do something here")
	sleep(5)
	run_this_function()

I haven't timed how long does it take for `subprocess.call` to start and end. Another quick check was testing if the output is produced before running the next line. This could be done in a unit test. Or like this:

	subprocess.call("do something and create some-file.csv")
	sleep(5)

	if 'some-file.csv' in os.listdir():
		run_this_function()