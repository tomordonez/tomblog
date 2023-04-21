---
layout: post
title: "Download Songs From Youtube"
description: "Download songs from Youtube."
author: tom
image: assets/images/1.jpg
tags: [linux, CLI, music]
---

Download songs from Youtube using Linux, Windows, or Mac.

Use at your own risk.

Tool: [doc here](https://github.com/yt-dlp/yt-dlp)

Follow the download instructions for Linux, Windows, or Mac.

	$ pip install -U yt-dlp

To update use the same command:

	$ pip install -U yt-dlp

Find the Youtube link to download. Say the link is `awesome-link`. Then download:

	$ yt-dlp -x --audio-format mp3 --audio-quality 0 --no-cache-dir "awesome-link"

If you want to download a playlist. Make a directory and download the files. Let's say the playlist is `awesome-playlist`:

	$ mkdir playlist
	$ cd playlist
	$ yt-dlp -i -o '%(playlist_index)s.%(ext)s' -x 
	--audio-format mp3 --audio-quality 0 --no-cache-dir "awesome-playlist"

The files will download like this:

	01.mp3  03.mp3  05.mp3
	02.mp3  04.mp3  06.mp3

If you want to merge the files into one big file. Use `cat` to concatenate all `mp3` files, then use `ffmpeg` to correct the metadata. If you don't fix this, the joined files will carry the time length of the first song, which will confuse the music player. Fixing this will add the times of all songs:

	$ cat *.mp3 > big.mp3
	$ ffmpeg -i big.mp3 big_fixed.mp3

**Downloads are slow**

Reinstall from the master branch:

    $ pip install --force-reinstall https://github.com/yt-dlp/yt-dlp/archive/master.tar.gz
