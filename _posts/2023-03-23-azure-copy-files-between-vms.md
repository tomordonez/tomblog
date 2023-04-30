---
layout: post
title: "**Unsolved**: In Azure Copying files between VMs"
author: tom
tags: [azure, virtual machine]
comments: true
---

**Unsolved**: In Azure, copying files between VMs doesn't work.

I couldn't figure out how to copy a file between two Azure VMs in the same Vnet using `Copy-Item` in PowerShell. I wanted to copy a file from a Windows Server VM (with open RDP) to a SQL Server VM (no RDP). This gives an error:

    Copy-Item -Path "C:\Users\...\Documents\sampledatabase.bk" -Destination "\\IP-Number\C$\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup"

Error:

    Copy-Item: The network path was not found
    CategoryInfo: NotSpecified: Copy-Item, IOException
    FullyQualifiedErrorId: System.IO.Exception

I think the error has to do with setting up the firewall on the SQL Server VM. There was a solution that recommended disabling the firewalls but I wasn't comfortable doing that.

Any ideas?

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)