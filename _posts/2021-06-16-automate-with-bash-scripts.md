---
layout: post
title: "Automate with Bash Scripts"
description: "Automate with Bash Scripts"
author: tom
image: assets/images/1.jpg
tags: [bash, linux, automate, taskwarrior]
---

This is how I automate using Bash scripts.

Linux is so powerful but sometimes it's hard to remember some commands.

For example, I use these lines to build and deploy my blog.

    $ bundle exec jekyll serve

If everything looks correct then I deploy like this:

    $ git add .
    $ git commit -m "Awesome commit message here"
    $ git push -u origin master
    $ JEKYLL_ENV=production jgd

Those are two scripts. One to build and one to deploy.

Instead of keeping scripts in separate files. I wanted to group them all and have an interface where to choose the script.

I automated these tasks:

* Check Memory Usage
* Check Disk Usage
* Build Tom's blog
* Deploy Tom's blog
* Launch IntelliJ
* View Tom's Tasks
* Download Music

Here is how it works:

![Automate with Bash Scripts]({{ site.baseurl }}/assets/images/automate-with-bash-scripts.gif)

## Creating an interactive menu in bash

I found an awesome article to create an interactive menu in bash [here](https://devdojo.com/bobbyiliev/how-to-create-an-interactive-menu-in-bash).

Create a master script called `menu`

    $ touch ~/bin/menu
    $ chmod u+x ~/bin/menu

The code has these sections:

* Functions to define child scripts
* Define color variables for the menu fonts
* Create a menu (main) function and use case for the menu options
* Call the menu function at the end

At the top of the file, the color variables are set to make the menu more visually appealing.

    # Color variables
    green='\e[32m'
    blue='\e[34m'
    red='\e[31m'
    clear='\e[0m'

    # Color functions
    ColorGreen(){
        echo -ne $green$1$clear
    }
    ColorBlue(){
        echo -ne $blue$1$clear
    }


At the bottom of the file the `menu` (main) function is defined like this example:

    menu(){
        echo -ne "
        Automate Tom's Life
        $(ColorGreen '1)') Check Memory
        $(ColorGreen '2)') Check Disk
        $(ColorGreen '3)') Build Blog
        $(ColorGreen '4)') Deploy Blog
        $(ColorGreen '5)') Launch IntelliJ
        $(ColorGreen '6)') View All Tasks
        $(ColorGreen '7)') Download Music
        $(ColorGreen '0)') Exit
        $(ColorBlue 'Choose an option:') "
        read a
        case $a in
            1) check_memory ; menu ;;
            2) check_disk ; menu ;;
            3) build_blog ; menu ;;
            4) deploy_blog ; menu ;;
            5) launch_intelliJ ; menu ;;
            6) view_tasks ; menu ;;
            7) download_music ; menu ;;
            0) exit 0 ;;
            *) echo -e $red"Wrong option."$clear; menu;;
        esac
    }

    # Call the menu function
    menu

Then define the functions after the color functions and before the `menu` function.

These functions check memory and disk.

    function check_memory() {
        free -h
    }

    function check_disk() {
        df -h
    }

I use this function to build my blog.

    function build_blog() {
        blogDirectory="/home/tom/blog"

        if [[ "$PWD" == $blogDirectory ]]; then

            echo -n "Build Tom's Blog? [y/n]: "
            read buildBlog

            if [[ $buildBlog == "y" ]]; then
              bundle exec jekyll serve
            else
              echo "Exiting"
            fi
        else
            echo -e $red"Wrong Blog Directory"$clear
        fi
    }

I use this to deploy my blog. It also asks the message for the git commit.

    function deploy_blog() {
        blogDirectory="/home/tom/blog"

        if [[ "$PWD" == $blogDirectory ]]; then

            echo -n "Deploy Tom's Blog? [y/n]: "
            read buildBlog

            if [[ $buildBlog == "y" ]]; then
              
              git add .
              
              echo -n "Enter commit message: "
              read commitMessage

              git commit -m \""$commitMessage"\"
              git push -u origin master
              JEKYLL_ENV=production jgd

            else
              echo "Exiting"
            fi
        else
            echo -e $red"Wrong Blog Directory"$clear
        fi
    }

This is used to launch IntelliJ in the background using the `&` symbol

    function launch_intelliJ() {
        nohup ~/Documents/idea-IU-211.7142.45/bin/idea.sh &
    }

This is how I view my todo tasks using the Taskwarrior app

    function view_tasks() {
        clear && task simple
    }

I am not a fan of streaming music apps because it clogs my memory and disk usage. I have a selected library of songs that I use to focus.

    function clean_song() {
        cat *.mp3 > big.mp3
        ffmpeg -i big.mp3 clean.mp3
        echo -ne $green"Rename song to (no spaces or file ext): "$clear
        read song_name
        mv clean.mp3 "$song_name".mp3
        mv "$song_name".mp3 ../
        rm *.mp3
    }

    function download_music() {
        (
            cd ~/Music/join/
            if [ "$(ls -A)" ]; then
                rm *.mp3
            fi
            echo -ne "Enter URL: "
            read url
            # Example playlist url: https://www.youtube.com/playlist?list=OLAK5uy_nPKXMNuvdxV1NJpPBpr3pd3tQ432eEwmE
            # Example song url: https://www.youtube.com/watch?v=F9edN2tPQVI
            if [[ "$url" =~ playlist ]]; then
                youtube-dl -i -o '%(playlist_index)s.%(ext)s' -x --audio-format mp3 --audio-quality 0 --no-cache-dir "$url"
                clean_song
            elif [[ "$url" =~ watch ]]; then
                youtube-dl -x --audio-format mp3 --audio-quality 0 --no-cache-dir "$url"
                clean_song
            else
                echo "Invalid URL"
            fi
        )
    }

Other use cases:

* Automate backing up files
* Send alert notifications to an email
* Manage tasks related to servers