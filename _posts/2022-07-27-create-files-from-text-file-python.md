---
layout: post
title: "Create Files From a Text File in Python"
description: "Automate creating files reading a text file containing filenames using Python."
author: tom
image: assets/images/2.jpg
tags: [python, automation]
comments: true
---

Automate creating files reading a text file containing filenames using Python.

I had a text file with blog post titles and filenames. I use a template to create blog posts. When I create only one blog post I just `cp template _posts/new-post.md` but now I had more than 10 posts to create.

The text file looked like this:

	An awesome blog post...
	an-awesome-blog-post.md

	Some other blog post
	some-other-blog-post.md

	...

	Yet another maybe 15th blog post
	yet-another-maybe-15th-blog-post.md

Use `shutil.copy2` to copy the template into the new file.

	import shutil

	with open('posts.txt', 'r') as fh:
	    for line in fh:
		    if '.md' in line:
			    shutil.copy2('template', line.rstrip('\r\n'))

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)