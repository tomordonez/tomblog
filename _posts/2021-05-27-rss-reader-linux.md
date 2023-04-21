---
layout: post
title: "RSS Reader for Linux"
description: "RSS Reader for Linux"
author: tom
image: assets/images/1.jpg
tags: [linux, RSS, reading]
---

RSS Reader for Linux.

Install in Fedora:

    $ sudo dnf install newsboat

Output:

    Installing:
     newsboat  x86_64  2.23-1.fc34    updates  2.5 M

    Installing dependencies:
     stfl      x86_64  0.22-37.fc34   fedora    50 k

    Total download size: 2.5 M
    Installed size: 10 M

Or Ubuntu:

    $ sudo apt install newsboat

Output:

    The following additional packages will be installed:
    libstfl0
    The following NEW packages will be installed:
    libstfl0 newsboat
    0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
    Need to get 2,028 kB of archives.
    After this operation, 10.7 MB of additional disk space will be used.

Run the program once:

    $ newsboat

Output:

    Starting newsboat 2.21.0...
    Loading configuration...done.
    Opening cache...done.
    Loading URLs from /home/your-user/.newsboat/urls...done.
    Error: no URLs configured. Please fill the file 
        /home/your-user/.newsboat/urls with RSS feed URLs 
        or import an OPML file.

The file `~/.newsboat/urls` will not exist so create one and add RSS feeds one per line. For example:

    https://hackaday.com/blog/feed/
    https://www.linuxjournal.com/node/feed
    https://www.linux.com/feed/

Modify the config file `~/.newsboat/config` as seen [here](https://forums.freebsd.org/threads/newsboat-rss-reader-enable-vim-key-bindings.69448/), to auto reload the feed, bind the arrow keys vim-style, and change the color theme. This file will not exist by default, so create one.

    # general settings
    auto-reload yes

    # unbind keys
    unbind-key j
    unbind-key k

    # bind keys for vim up/down
    bind-key j down
    bind-key k up

    # solarized

    color background         default   default
    color listnormal         default   default
    color listnormal_unread  default   default
    color listfocus          black     cyan
    color listfocus_unread   black     cyan
    color info               default   black
    color article            default   default

    # highlights
    highlight article "^(Title):.*$" blue default
    highlight article "https?://[^ ]+" red default
    highlight article "\\[image\\ [0-9]+\\]" green default

    # browser
    browser "firefox %u"


Load the program again:

    $ newsboat

Select a feed by pressing `Enter`. Use the `q` (quit) to return to the previous screen.

If you quit, you will see the output again:

    Starting newsboat 2.21.0...
    Loading configuration...done.
    Opening cache...done.
    Loading URLs from /home/your-user/.newsboat/urls...done.
    Loading articles from cache...done.
    Cleaning up cache...done.

Sources:
* [Newsboat Docs](https://newsboat.org/releases/2.23/docs/newsboat.html)
* [Newsboat Github](https://github.com/newsboat/newsboat)
* [Newsboat Vim bindings - Freebsd](https://forums.freebsd.org/threads/newsboat-rss-reader-enable-vim-key-bindings.69448/)