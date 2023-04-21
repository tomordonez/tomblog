---
layout: post
title: "Play Music From The Command Line"
description: "Play music from the command line. Command line music player in Linux."
author: tom
image: assets/images/1.jpg
tags: [linux, CLI, music]
---

Play music from the command line with this music player in Linux.

## CMUS command line music player

Install with:

    $ sudo dnf install cmus

If this shows in Fedora:

    Error: Unable to find a match: cmus

Then install RPMFusion as shown in the [docs](https://rpmfusion.org/Configuration).

For the free repo:

    sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm

Then try again:

    $ sudo dnf install cmus

Output:

    Installing:
    cmus  2.9.1-1.fc33  323 k

    Installing dependencies:
    libmp4v2  2.1.0-0.22.trunkREV507.fc33  439 k

    Install  2 Packages

    Total download size: 762 k
    Installed size: 2.2 M

    Running transaction
    Preparing:
    Installing: libmp4v2-2.1.0-0.22.trunkREV507.fc33.x86_64
    Installing: cmus-2.9.1-1.fc33.x86_64
    Running scriptlet: cmus-2.9.1-1.fc33.x86_64
    Verifying: libmp4v2-2.1.0-0.22.trunkREV507.fc33.x86_64
    Verifying: cmus-2.9.1-1.fc33.x86_64

    Installed:
    cmus-2.9.1-1.fc33.x86_64  libmp4v2-2.1.0-0.22.trunkREV507.fc33.x86_64

## Music Player Size Comparison

Here is a comparison of installed sizes of different music players:

* CMUS: 2.2 M
* Rhythmbox: 2.26 M
* Winamp: 16 M
* Spotify: 44 M
* iTunes: 400 M

## Starting CMUS

Start with:

    $ cmus

Once you open it for the first time, it creates a config file here:

    ~/.config/cmus/

    $ ls ~/.config/cmus/
    playlists

Opening `CMUS` for the first time is similar to opening Vim. There is nothing to see and you cannot figure out from this screen how to use it.

Below is a summary of the official docs [here](https://cmus.github.io/#documentation)

## Navigating CMUS

CMUS is similar to Vim. You have to know the keyboard shortcuts or commands to use the interface.

Navigate different views by pressing numbers from 1 to 7.

As seen in the docs:

* `1`: Library view. All songs added to the library.
* `2`: Sorted library view. Save as view 1 but sorted.
* `3`: Playlist view. Editable with optional sorting.
* `4`: Play queue view. Upcoming songs to play. They are played before anything else.
* `5`: Directory browser. Add music for the library, playlist, or queue.
* `6`: User defined filters.
* `7`: Settings/keybindings.

## Add songs to the library

Press the key `5` and navigate to your Music directory. Then press `a` to add songs to your library. There is no confirmation message when you add songs. The only indicator is that pressing `a` will move to the next line. I just kept the `a` pressed until it reached the end of the directory.

Like in Vim you can enter commands with the colon syntax. Use this to save your settings: `:save`.

A message at the bottom says:

    :save only works in views 1 - 4

Navigate the other views to see what they have.

* View 1 has a split screen with Artist/Album on the left and the song files on the right.
* View 2 has the song files sorted.
* View 3 has an empty split screen with `Playlist *default` on the left and `Track` on the right.
* View 4 says `Play Queue - 0 tracks`.

Let's try to save in View 4:

    :save

No luck there. Got this message:

    Error: need a file as argument, no default stored yet

Jump to View 1:

    :save

No error. It is saved.

## Playing music

Since View 1 is split in two. You can use `tab` to move between sides. Then up/down. You can play songs from View 1 or View 2.

Move to View 2.

Press `Enter` to play a song.

The bottom of the screen shows the playing time like this:

    song.mp3
    > 01:01 / 45:06

Press `c` to pause/play the song.

The bottom right of the screen shows this:

    all from library | C

You can change these settings by pressing `m` which will change `all from library` to other options.

To the right where the `C` is you can change these settings:

* Continue with `shift C`
* Repeat with `r`
* Shuffle with `s`
* Follow with `f`

You can experiment with these to see what they do.

You can add songs to a queue without interrupting the current song. While in View 2 (just press `2`). Tap `e` to add a song to the queue. Then switch to View 4 to see the Queue. You can pause the song from this view as well by tapping `c`. Change the order of the songs in the queue by pressing `p`.

Not very intuitive. I would change the keybinding so that `p` is pause. Changing the order of the queue moving a song up with `u` and down with `d`. Add a song to the queue with `a`.

## Create a playlist

I use music apps mostly in playlists. For example I have a playlist called `focus`.

Go to View 2 and use `y` to add songs to a playlist.

Go to View 3 (press `3`) to view the playlist. This view is also split in two.

The left side says this:

    Playlist
    * default

Move between sides with `tab`. Then you can use `p` to move a song up or `P` to move it down.

Switch to the left side with `tab` to select the playlist name `default`. Then type the command to rename it. Like this:

    :pl-rename focus

The left side now says:

    Playlist
    * focus

Switch to the right side with `tab` and press `Enter` on one of the songs to start playing the list.

Remove songs from the playlist with upper case `D`.


## More

Sources:

* [CMUS doc1](https://github.com/cmus/cmus/blob/master/Doc/cmus-tutorial.txt)
* [CMUS doc2](https://github.com/cmus/cmus/blob/master/Doc/cmus.txt)
* [CMUS doc3](https://github.com/cmus/cmus/wiki/reviews)
* [CMUS main page](https://cmus.github.io/#home)