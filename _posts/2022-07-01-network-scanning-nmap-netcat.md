---
layout: post
title: "Network Scanning with nmap and netcat in Kali"
description: "Learning network scanning with nmap and netcat"
author: tom
image: assets/images/1.jpg
tags: [network security, pentesting, nmap, netcat, kali linux, kali, linux]
comments: true
---

Learning network scanning with nmap and netcat in Kali Linux.

## Setup VirtualBox

Install VirtualBox v6 or later. Download and install Extension Pack and import it in `Tools/Preferences/Extensions`

Create a NAT network in `Tools/Preferences/Network`. For this exercise I named it `natscan` (rename it if it creates a default name)

## Setup Kali Linux

Go to Kali Linux and download the image for VirtualBox. Import the image to VBox.

### In the settings (System/Processor):

* Increase the CPUs and RAM.
* Set CPUs to 2 and RAM to 4048MB.

### In Display:

* Set Enable 3D Acceleration.

### In Network:

* Adapter 1. Attached to NAT. Change to NAT Network. In Name, select `natscan` from the dropdown.
* Adapter type. Select one of the Server options.

### Setup shared folder host/guest in Kali VM:

* Enter the default user/pwd: `kali/kali`
* On Kali's logo menu top left go to `Settings/Display`, change resolution to `1920x1080`
* On Kali's Virtualbox menu go to `Devices/Shared Folders/Shared Folder Settings`. Select `Machine Folders` and add a folder. Select path on the host computer and/or create a folder (such as `Documents/kali/`) which will be shared with the Kali guest. Select `Auto-mount` and `Make Permanent` then click OK.
* On Kali's terminal add Kali's user to the `vboxsf` group: `sudo usermod -a -G vboxsf kali`
* Create a file in the shared folder in Kali `/media/sf_kali/` to see that is shared with the host: `touch /media/sf_kali/it_works.txt` then navigate to the host `Documents/kali` and the txt file should appear there.

## Setup a vulnerable machine

Here is a list of options provided by Rapid 7 for places where to find vulnerable machines for your lab. More [here](https://www.rapid7.com/blog/post/2011/12/23/where-can-i-find-vulnerable-machines-for-my-penetration-testing-lab/)

Import the VM and change the Network Adapter to the NAT Network created. In this case `natscan`. Select the same adapter type as the one in Kali VM. Then headless start the VM.

## Use nmap to scan the network

More details in `man nmap`. Nmap (Network Mapper) is a tool for network exploration and security auditing. It can scan large networks to find hosts, services, OS versions, firewalls, etc. The output is a list of scanned targets including port, state, service, and version. Follow the official docs on the legal issues of port scanning with nmap [here](https://nmap.org/book/legal-issues.html)

For example, this uses `-A` to find OS, version, script scanning, and traceroute, and `-T4` for faster execution:

	$ nmap -A -T4 scanme.nmap.org

Output:

	Nmap scan report for scanme.nmap.org (74.207.244.221)
    Host is up (0.029s latency).
    rDNS record for 74.207.244.221: li86-221.members.linode.com
    Not shown: 995 closed ports
    PORT     STATE    SERVICE     VERSION
    22/tcp   open     ssh         OpenSSH 5.3p1 Debian 3ubuntu7 (protocol 2.0)
	80/tcp   open     http        Apache httpd 2.2.14 ((Ubuntu))
    646/tcp  filtered ldp
    1720/tcp filtered H.323/Q.931
    9929/tcp open     nping-echo  Nping echo

More about `-T4` according to the docs:. `-T` is used to set a timing template:

* `-T0` (paranoid), `-T1` (sneaky): Used for IDS (intrusion detection system) evasion.
* `-T2` (polite): Slows down the scan to use less bandwidth and target machine resources.
* `-T3` (normal): It's the default mode, it doesn't do anything.
* `-T4` (aggressive): Speeds scans assuming you are on a fast and reliable network.
* `-T5` (insane): Assumes you are on a really fast network or willing to sacrifice accuracy for speed.

More about `-A` (aggressive scan). It enables these options:

* `-O`: OS detection
* `-sV`: version scanning
* `-sC`: script scanning (considered intrusive)
* `--traceroute`

## Find the IP of the vulnerable VM

Find Kali's VM IP number:

	$ ifconfig

Output:

	eth0:
	inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255

Scan the network:

	$ nmap -T4 10.0.2.0/24

Output:

	Nmap scan report for 10.0.2.1
	Host is up (0.0013s latency).
	Not shown: 999 closed tcp ports (conn-refused)
	PORT   STATE SERVICE
	53/tcp open  domain

	...

	Nmap scan report for 10.0.2.4
	Host is up (0.0019s latency).
	Not shown: 992 closed tcp ports (conn-refused)
	PORT     STATE SERVICE
	21/tcp   open  ftp
	22/tcp   open  ssh
	23/tcp   open  telnet
	53/tcp   open  domain
	80/tcp   open  http
	3128/tcp open  squid-http
	8080/tcp open  http-proxy
	9099/tcp open  unknown

	...

	Nmap done: 256 IP addresses (4 hosts up) scanned in 3.37 seconds

More about `STATE`:

* The state can be `open`, `filtered`, `closed`, or `unfiltered`
* `open`: An application on the target is listening for connections on that port
* `filtered`: A firewall/obstacle is blocking the port and nmap can't tell if it's open or closed
* `closed`: No applications listening
* `unfiltered`: Responsive to nmap probing but nmap can't tell if port is open or closed

## Find ports and service version of the target

Use `-p-` to scan ports from 1 to 65535, which is equivalent to using `-p1-65535` but omitting the start and end ranges. Use `-sV` for version detection.

	$ nmap -p- -sV 10.0.2.4

Output:

	Host is up (0.0012s latency).
	Not shown: 65523 closed tcp ports (conn-refused)
	PORT     STATE SERVICE    VERSION
	21/tcp   open  ftp        vsftpd 2.3.5
	22/tcp   open  ssh        OpenSSH 5.9p1 Debian 5ubuntu1.1
	23/tcp   open  telnet     Linux telnetd
	53/tcp   open  domain     ISC BIND 9.8.1-P1
	80/tcp   open  http       Apache httpd 2.2.22 ((Ubuntu))
	3128/tcp open  http-proxy Squid http proxy 3.1.19
	8080/tcp open  http       Apache httpd 2.2.22 ((Ubuntu))
	9095/tcp open  unknown

## Using netcat

See the docs with `man nc`.

`netcat` is a tool that reads and writes data across network connections using TCP or UDP. Use like this: `nc host port` to create a TCP connection with `host` on a `port` number. STDIN is sent to the host and any response is sent to STDOUT until the network side of the connection shuts down. Compared to `telnet` where it shuts down after an EOF on STDIN.