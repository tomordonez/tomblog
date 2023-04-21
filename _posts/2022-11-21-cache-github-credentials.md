---
layout: post
title: "Cache Github Credentials"
description: "Caching Github credentials on Linux"
author: tom
image: assets/images/1.jpg
tags: [git, github]
comments: true
---

Caching Github credentials on Linux.

My workflow is:
* Restrict access to main
* Create a branch
* Push changes to Github using the command line.
* It asks for user and token

However, when making many commits, it gets annoying really quick, everytime
entering the user/token combination.

From the official docs on Github
[here](https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git)
on caching Github credentials, I followed managing this with Github CLI.

Installed Github CLI using conda as I had this already. Although, first I
updated conda:

    $ conda update -n base -c defaults conda3

    $ conda install gh --channel conda-forge

Output:

    Downloading and Extracting Packages
    python_abi-3.9       | 4 KB
    gh-2.20.2            | 15.1 MB
    ca-certificates-2022 | 150 KB
    certifi-2022.9.24    | 155 KB
    conda-22.9.0         | 963 KB

Then configure credentials:

    $ gh auth login

Output:

    ? What account do you want to log into? GitHub.com
    ? What is your preferred protocol for Git operations? HTTPS
    ? Authenticate Git with your GitHub credentials? Yes
    ? How would you like to authenticate GitHub CLI? Login with a web browser

It asks to open web browser and enter a code given in the terminal:

    ! First copy your one-time code: (an 8 digit code here)
    Press Enter to open github.com in your browser... 
    Gtk-Message: Not loading module "atk-bridge": The functionality is provided
    by GTK natively. Please try to not load it.

Enter the code in the browser, then authenticate which account to use and giver
permission. Back in the terminal:

    ✓ Authentication complete.
    - gh config set -h github.com git_protocol https
    - ✓ Configured git protocol
    - ✓ Logged in as (my username)

Caching Github credential with IntelliJ follows a similar authentication that
opens the web browser and you allow IntelliJ access to your Github account.

* In IntelliJ go to File/Settings/Version Control/Github
* Add account
* Then follow the browser instructions
