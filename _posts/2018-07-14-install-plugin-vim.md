---
layout: post
title: "Install a Plugin in Vim"
redirect_from:
  - /install-plugin-vim.html
categories: [Code]
image: assets/images/3.jpg
tags: [vim, vim plugin]
---

How to install a plugin in Vim using Vundle.

If you don't have vim install, then use either `sudo apt install vim` or `sudo dnf install vim` or on mac I bet brew is still around.

Clone the Vundle repo to the `.vim` directory.

    git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

If you don't have a vim config file, then create one with either `touch .vimrc` or `vim .vimrc`.

When editing a `.vimrc` always source it while inside vim with `:so %` otherwise sourcing in bash with `source .vimrc` might show errors.

Edit the `.vimrc` file and add these lines:

    set nocompatible
    filetype off
    " set the runtime path to include Vundle and initialize
    set rtp+=~/.vim/bundle/Vundle.vim
    call vundle#begin()

    " let Vundle manage Vundle, required
    Plugin 'VundleVim/Vundle.vim'

    " All of your Plugins must be added before the following line
    call vundle#end()            " required
    filetype plugin indent on    " required

## Install a Vim Plugin

1. Go to a plugin that says you can install using `Vundle`.
2. For instance. This plugin auto saves when you are editing a file: <a href="https://github.com/907th/vim-auto-save" target="_blank">auto-save</a>.
3. The Github project should have a directory called `plugin`.
4. Add the `user/project` from Github to your `.vimrc` file. In the `auto-save` example. The user is `907th` and the project is `vim-auto-save`. Then add this line to `.vimrc` file: `Plugin '907th/vim-auto-save'`
5. Save `vimrc`. Then source it with `:so %`.
6. List all `Vundle` plugins while inside Vim: `:PluginList`. Make sure the Plugin is listed. You can do `:q` to exit this view.
7. Then install using `:PluginInstall`. This will install recently added Plugins.

When adding a Vim plugin while inside Vim, always list and install using these commands. The cursor will move down the list installing each new plugin:

    :PluginList
    :PluginInstall

This particular `Plugin` for `auto-save` had a few more lines that had to be added. Depending on the `Plugin` readme page, please follow those specific instructions.

For `auto-save`. You need to add these lines to the `.vimrc` file:

    let g:auto_save = 1
    let g:auto_save_in_insert_mode = 0

These are Vim plugins I installed:

Markdown with correct code highlight

    Plugin 'plasticboy/vim-markdown'

A better JSON for Vim as seen on:

    Plugin 'elzr/vim-json'

Color schemes

    Plugin 'flazz/vim-colorschemes'

File Tree

    Plugin 'preservim/nerdtree'

Autosave files after exiting insert mode:

    Plugin '907th/vim-auto-save'
    let g:auto_save = 1
    let g:auto_save_in_insert_mode = 0