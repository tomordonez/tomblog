---
layout: post
title: "Markdown to HTML to PDF"
description: "Preview markdown in HTML then print to PDF"
author: tom
image: assets/images/1.jpg
tags: [linux, markdown]
---

Preview markdown in HTML then print to PDF.

Install `grip` (based on Flask):

    $ pip install grip

Open your markdown file in the browser:

    $ grip markdown_file.md

This runs a web server and it gives you the localhost link to open.

    * Serving Flask app "grip.app" (lazy loading)
    * Environment: production
      WARNING: This is a development server. Do not use it in a production deployment.
      Use a production WSGI server instead.
    * Debug mode: off
    * Running on http://localhost:6419/ (Press CTRL+C to quit)

Updates in your markdown file are refreshed. Preview in the browser and print PDF from
there.

References:
* [Grip Doc](https://github.com/joeyespo/grip)
* [Convert Markdown to PDF](https://superuser.com/questions/689056/how-can-i-convert-github-flavored-markdown-to-a-pdf)