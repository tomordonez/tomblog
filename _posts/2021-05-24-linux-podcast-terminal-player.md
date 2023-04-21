---
layout: post
title: "Linux Podcast Terminal Player"
description: "How to play podcasts on the terminal in Linux"
author: tom
image: assets/images/1.jpg
tags: [linux, podcast]
---

How to play podcasts on the terminal in Linux.

    $ pip install castero

<details>
    <summary>Output</summary>
    <pre>

    Defaulting to user installation because normal site-packages is not writeable
    Collecting castero
      Downloading castero-0.9.2-py3-none-any.whl (63 kB)
         |████████████████████████████████| 63 kB 495 kB/s 
    Requirement already satisfied: lxml in /usr/lib64/python3.9/site-packages (from castero) (4.6.2)
    Collecting cjkwrap
      Downloading CJKwrap-2.2-py2.py3-none-any.whl (4.3 kB)
    Collecting grequests
      Downloading grequests-0.6.0-py3-none-any.whl (5.2 kB)
    Collecting python-vlc
      Downloading python_vlc-3.0.12118-py3-none-any.whl (79 kB)
         |████████████████████████████████| 79 kB 1.0 MB/s 
    Collecting python-mpv
      Downloading python-mpv-0.5.2.tar.gz (25 kB)
    Requirement already satisfied: beautifulsoup4 in /usr/lib/python3.9/site-packages (from castero) (4.9.3)
    Requirement already satisfied: requests in /usr/lib/python3.9/site-packages (from castero) (2.25.1)
    Requirement already satisfied: soupsieve>1.2 in /usr/lib/python3.9/site-packages (from beautifulsoup4->castero) (2.2)
    Collecting gevent
      Downloading gevent-21.1.2-cp39-cp39-manylinux2010_x86_64.whl (6.0 MB)
         |████████████████████████████████| 6.0 MB 2.8 MB/s 
    Collecting zope.event
      Downloading zope.event-4.5.0-py2.py3-none-any.whl (6.8 kB)
    Requirement already satisfied: setuptools in /usr/lib/python3.9/site-packages (from gevent->grequests->castero) (53.0.0)
    Collecting zope.interface
      Downloading zope.interface-5.4.0-cp39-cp39-manylinux2010_x86_64.whl (255 kB)
         |████████████████████████████████| 255 kB 9.0 MB/s 
    Collecting greenlet<2.0,>=0.4.17
      Downloading greenlet-1.1.0-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (162 kB)
         |████████████████████████████████| 162 kB 9.0 MB/s 
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /usr/lib/python3.9/site-packages (from requests->castero) (1.25.10)
    Requirement already satisfied: idna<3,>=2.5 in /usr/lib/python3.9/site-packages (from requests->castero) (2.10)
    Requirement already satisfied: chardet<5,>=3.0.2 in /usr/lib/python3.9/site-packages (from requests->castero) (4.0.0)
    Using legacy 'setup.py install' for python-mpv, since package 'wheel' is not installed.
    Installing collected packages: zope.interface, zope.event, greenlet, gevent, python-vlc, python-mpv, grequests, cjkwrap, castero
        Running setup.py install for python-mpv ... done
    Successfully installed castero-0.9.2 cjkwrap-2.2 gevent-21.1.2 greenlet-1.1.0 grequests-0.6.0 python-mpv-0.5.2 python-vlc-3.0.12118 zope.event-4.5.0 zope.interface-5.4.0

    </pre>

</details>

Open the interface:

    $ castero

It looks like this:

![Image Name]({{ site.baseurl }}/assets/images/linux-terminal-podcast-player.jpg)

There are three viewing modes. Type a number:

* 1
* 2
* 3

Type `h` for help.

Navigate with arrows left, right, up, down.

Add a podcast by typing `a`. Enter the RSS link.

Play an episode with `Enter`.

Pause with `p`.

Remove an annoying podcast with `d`.

Source:

* [Castero Github repo](https://github.com/xgi/castero)