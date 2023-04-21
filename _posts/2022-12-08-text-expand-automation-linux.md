---
layout: post
title: "Text Expand Automation in Linux"
description: "An alternative to TextExpander to expand text and other automations in Linux."
author: tom
image: assets/images/1.jpg
tags: [autokey, texpander, text expand, linux]
comments: true
---

An alternative to TextExpander to expand text and other automations in Linux.

## AutoKey

More about Autokey in the repo [here](https://github.com/autokey/autokey). It uses X-org. If you are on Wayland, you need to switch to X or it won't work.

In Ubuntu install using `sudo apt install autokey-gtk`.

Open Autokey and see the example with the abbreviation `adr`. In a text editor, type that abbreviation and it should expand the example text.

This program is very glitchy and it often doesn't work, after quitting and opening a few times. Sometimes it expands the abbreviation but often you don't see anything.

## Texpander

This is the one I use, it's very light and easier. It also requires X-org. It doesn't have a GUI to set it up. But if you are in Linux, then this should be easy. The repo is [here](https://github.com/leehblue/texpander). Follow these steps.

1. Download `texpander.sh` to `~/bin`
2. Set the file to executable `chmod +x ~/bin/texpander.sh`
3. Create a directory `~/.texpander`
4. Create a custom shortcut to run the shell file. In Ubuntu:
    - Settings > Keyboard > Keyboard Shortcuts
    - View and Customize Shortcuts
    - Scroll down to Custom Shortcuts
    - Add
    - Name: Text Expand
    - Command: `/home/your-user/bin/texpander.sh`
    - Shortcut: `Ctrl + Enter`
5. Install the required packages `sudo apt install xsel xdotool zenity`

Then create the file with the text you want to expand. For example:

    echo "Homer Simpson" > ~/.texpander/name.txt

Then activate the script wherever you want to type, for example, go to a text editor.

* Open text editor (or email or browser, etc)
* Type the shortcut `Ctrl + Enter`
* A menu opens with `name.txt` as one of the options. Click on that.
* The text editor will type the content of that file.

This process is basically a geeky copy/paste. However after using the paid software Text Expander in the past. You can setup all sorts of text expanding automations that would save you hours of repetitive manual work every day.
