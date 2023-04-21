---
layout: post
title: "Linux History with Date Timestamp"
description: "Set Linux History command with Date Timestamp"
author: tom
image: assets/images/1.jpg
tags: [linux, terminal]
---

Set Linux History command with Date Timestamp.

As seen in Ask Ubuntu [here](https://askubuntu.com/questions/391082/how-to-see-time-stamps-in-bash-history). You can setup your `bashrc` with `HISTTIMEFORMAT` to get the linux history with date timestamp. However, the original dates will not show up for commands you previously ran. The date will apply from the current timestamp when you configured this setting.

Edit your `~/.bashrc` file and add this line:

    HISTTIMEFORMAT="%F %T "

Source the file:

    $ source ~/.bashrc

Test:

    $ history

Output:

    6315  2021-05-31 20:27:34 man histtimeformat
    6316  2021-05-31 20:27:34 vim ~/.bashrc
    6317  2021-05-31 20:27:33 source ~/.bashrc
    6318  2021-05-31 20:27:35 history