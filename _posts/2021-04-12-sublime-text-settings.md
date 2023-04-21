---
layout: post
title: "Sublime Text Settings"
description: "These are my Sublime Text settings"
author: tom
image: assets/images/1.jpg
tags: [linux, sublimetext]
---

These are my Sublime Text settings.

I run the default theme with some modifications:

Add these to Preferences/Settings:

    {
        "color_scheme": "Packages/User/Color Highlighter/themes/Monokai Extended.tmTheme",
        "font_size": 13,
        "open_files_in_new_window": false,
        "preview_on_click": false,
        "save_on_focus_lost": true,
        "show_definitions": false,
        "translate_tabs_to_spaces": true,
        "enable_tab_scrolling": false
    }


Here is more detail on these settings.

Open files in new tabs:

    "open_files_in_new_window": false,
    "preview_on_click": false,

Autosave files:

    "save_on_focus_lost": true,

Tabs to spaces:

    "translate_tabs_to_spaces": true,

Resize the width of file tabs so that you can see all the tabs that are open:

    "enable_tab_scrolling": false

## Sublime Text Packages

I have a few packages installed:

* AutoFileName
* BracketHiglighter
* Javascript Beautify
* Pretty JSON
* sublack
* Typewriter (in scrolling mode)
* UnicodeMath
* Word Count
* HTML/CSS/JS Prettify
* JsPrettier
* ColorPicker

## Edit the color scheme for markdown

See the Sublimetext docs for scope naming
[here](https://www.sublimetext.com/docs/scope_naming.html#markup)

Go Preferences/Customize Color Scheme.

On the right side there is room to add rules.

For example, if you want to change the font color of inline code blocks.

    "rules":
      [
        {
          "scope": "markup.raw.inline",
          "foreground": "#ff2a27"
        }
      ]

Use the `foreground` to change the font color.
