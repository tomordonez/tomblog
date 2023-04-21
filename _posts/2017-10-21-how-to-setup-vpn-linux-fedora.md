---
layout: post
title: "How To Setup a VPN in Linux Fedora"
redirect_from:
  - /how-to-setup-vpn-linux-fedora.html
categories: [Linux]
image: assets/images/10.jpg
tags: [vpn, linux, fedora, openvpn]
---

Updated post to setup a VPN in Linux with NordVPN.

1. Get a NordVPN account
2. Use the terminal to download the app
3. Restart
4. Use the terminal to login, authenticate on the web
5. Use the terminal to connect

## Use the terminal to download the app

As seen in the docs run:

    $ sh <(curl -sSf https://downloads.nordcdn.com/apps/linux/install.sh)

Output

    Adding repo from: https://repo.nordvpn.com//yum/nordvpn/centos/x86_64
    NordVPN YUM repository - x86_64
    NordVPN YUM repository - noarch
    created by dnf config-manager from 
     https://repo.nordvpn.com//yum/nordvpn/centos/x86_64
    Dependencies resolved.
    Installing:
     nordvpn  x86_64   3.12.2-1   22 M

    Total download size: 22 M
    Installed size: 65 M
    Downloading Packages:
    nordvpn-3.12.2-1.x86_64.rpm
    Running transaction
      Preparing        :
      Installing       : nordvpn-3.12.2-1.x86_64
      Running scriptlet: nordvpn-3.12.2-1.x86_64

    Adding user to the group nordvpn
    NordVPN for Linux successfully installed!
    To get started, please re-login or execute `su - $USER` in the current shell, type 'nordvpn login' and enter your NordVPN account details. Then type 'nordvpn connect' and you’re all set! To allow other users to use the application run 'usermod -aG nordvpn otheruser'. If you need help using the app, use the command 'nordvpn --help'.
      Verifying        : nordvpn-3.12.2-1.x86_64
    Installed:
      nordvpn-3.12.2-1.x86_64
    Complete!

Run to add your user then restart

    $ sudo usermod -aG nordvpn $USER

## Use the terminal to login, authenticate on the web

    $ nordvpn login

Open the generated link on the browser, authenticate, then go back to the terminal.

## Use the terminal to connect

Before connecting, verify your current IP and see the output. Then connect to the VPN and verify the new IP

    $ nordvpn connect

To disconnect just use:

    $ nordvpn disconnect

To get the docs use:

    $ nordvpn --help

## IPv6 Issues

In the US. Comcast has IPv6 enabled and this can be an issue.

The best solution is to login to your router. Disable IPv6 and enable IPv4.

If this is not possible. You can try to disable IPv6 from the command line. But if your router doesn't have IPv4 enabled this will not work:

To disable IPv6 from terminal:

    sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1

To re-enable IPv6:

    sudo sysctl -w net.ipv6.conf.all.disable_ipv6=0

Or edit the file directly with:

    sudo vim /etc/sysctl.conf

You can also disable IPv6 from your laptop Wifi Settings.

If you disable IPv6 and you lose internet. Try restarting your laptop Wifi or Wifi card.

## Other issues

The VPN doesn't connect or shows output that a new version is available

    $ sudo dnf update nordvpn

If that doesn't work, restart the service

    $ sudo systemctl stop nordvpnd.service
    $ sudo systemctl start nordvpnd.service