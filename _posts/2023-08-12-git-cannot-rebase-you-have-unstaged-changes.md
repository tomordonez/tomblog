---
layout: post
title: "Git cannot rebase, you have unstaged changes"
description: "After git rebase origin/main, the error says cannot rebase, you have unstaged changes"
author: tom
image: assets/images/1.jpg
tags: [git]
comments: true
---

After git rebase origin/main, the error says cannot rebase, you have unstaged changes.

Something got messed up after I moved from Linux to Windows. After cloning my [Learning](https://github.com/tomordonez/learning) repo, I couldn't refresh my local repo for a directory I had in gitignore.

	$ git rebase origin/main
	error: cannot rebase. You have unstaged changes.
	error: Please commit or stash them

There were changes I did not want to keep for a file called `run.sh`. This didn't work

	$ git restore run.sh

It got stuck on the same error message.

Solution

	$ git rm --cached run.sh
	$ git commit -m "Untrack run.sh"

Then rebase again

	$ git rebase origin/main
	Successfully rebased and updated refs/heads/...