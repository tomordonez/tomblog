---
layout: post
title: "Resize an image in Linux"
description: "How to resize an image in Linux with convert from ImageMagick"
author: tom
image: assets/images/1.jpg
tags: [linux, linkedin]
comments: true
---

How to resize an image in Linux with convert from ImageMagick.

Use the command `convert`.

If you get `command not found` opt to install the package if it shows `Install package 'ImageMagick' to provide command 'convert'` or using `sudo apt install imagemagick`.

The output will show something similar to this:

	The following packages have to be installed:
	 ImageMagick-1:6.9.12.50-1.fc36.x86_64  An X application for displaying and manipulating images
	 ImageMagick-libs-1:6.9.12.50-1.fc36.x86_64     ImageMagick libraries to link with
	 graphviz-2.50.0-6.fc36.x86_64  Graph Visualization Tools
	 gts-0.7.6-41.20121130.fc36.x86_64      GNU Triangulated Surface Library
	 lasi-1.1.3-8.fc36.x86_64       C++ library for creating Postscript documents
	 libXaw-1.0.14-2.fc36.x86_64    X Athena Widget Set
	 liblqr-1-0.4.2-19.fc36.x86_64  LiquidRescale library
	 libwmf-lite-0.2.12-8.fc36.x86_64       Windows Metafile parser library
	 mkfontscale-1.2.1-4.fc36.x86_64        Tool to generate legacy X11 font system index files
	 netpbm-10.97.00-2.fc36.x86_64  A library for handling different graphics file formats
	 xorg-x11-fonts-ISO8859-1-100dpi-7.5-33.fc36.noarch     A set of 100dpi ISO-8859-1 fonts for X

After installing, it might show:

	Version: ImageMagick 6.9.12-50 Q16 x86_64 17254 https://legacy.imagemagick.org
	Copyright: (C) 1999 ImageMagick Studio LLC
	License: https://imagemagick.org/script/license.php
	Features: Cipher DPC Modules OpenMP(4.5)
	Delegates (built-in): bzlib cairo djvu fontconfig freetype gslib gvc jbig jng jp2 jpeg lcms lqr ltdl lzma openexr pangocairo png ps raqm raw rsvg tiff webp wmf x xml zlib
	Usage: convert [options ...] file [ [options ...] file ...] [options ...] file

You can also see the docs with either of these commands

	$ man convert
	$ convert -help

I use `convert` to resize a screenshot of a burndown chart from Jira to fit the dimensions of the Linkedin profile header.

Use the exclamation point to fix the width and height, otherwise it will autoscale the height.

	$ convert screenshot.png -resize 1584x396! li-background.png
