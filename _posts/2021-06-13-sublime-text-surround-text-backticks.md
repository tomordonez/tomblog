---
layout: post
title: "Sublime Text Surround Text in Backticks"
description: "Sublime Text Surround Text in backticks or quotes"
author: tom
image: assets/images/1.jpg
tags: [sublimetext]
---

Sublime Text Surround Text in backticks or quotes.

For `'` single quotes or `"` double quotes.

Select the text and type either character to enclose the selected text with that character.

Enclosing with backtick works by adding this code to `Preferences -> Key Bindings` as seen on this [gist](https://gist.github.com/yuanqing/87bceacedde4dbd96fab)


    { "keys": ["`"], "command": "insert_snippet", "args": {"contents": "`${0:$SELECTION}`"}, "context":
      [
        { "key": "setting.auto_match_enabled", "operator": "equal", "operand": true },
        { "key": "selection_empty", "operator": "equal", "operand": false, "match_all": true }
      ]
    }

You can also select multiple words and apply the character to surround it.

![Sublime Text Surround Text in Backticks]({{ site.baseurl }}/assets/images/sublime-text-surround-text-backticks.gif)