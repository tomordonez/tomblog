---
layout: post
title: "Use Chocolatey in Windows Powershell to Install Programs"
author: tom
tags: [chocolatey, powershell, windows server]
comments: true
---

Chocolatey is the Windows version of Linux's `apt` or `dnf`.

Open Administrator Powershell.

## Set up $profile in PowerShell

See the contents of `$profile`:

    Write-Host $profile

This only shows the variable's content but the file doesn't exist there:

    C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

Create the file as seen in the Microsoft docs [here](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-item?view=powershell-7.3):

    New-Item -Path $profile -ItemType "file" -Force

## Install Chocolatey

Install Chocolatey as seen in the official docs [here](https://docs.chocolatey.org/en-us/choco/setup).

    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

Even though, the docs don't fully explain exactly what this does. Here is a short summary form ChatGPT:

    ...this command sets up the PowerShell environment to allow the execution of scripts without any restrictions, updates the security protocol used for connecting to remote servers, and then downloads and installs the Chocolatey package manager using a script from a specific URL.

More detail about every command:

* `Set-ExecutionPolicy Bypass`: Sets PowerShell execution policy to Bypass for the current process
* `-Scope Process`: Only apply to the current PowerShell session
* `-Force`: Supress any confirmation prompt
* `-bor`: Bitwise OR operator
* `3072`: Represents the TSL 1.2 security protocol
* `[System.Net.ServicePointManager]::SecurityProtocol -bor 3072`: Bitwise OR between the current security protocol and TSL 1.2 to allow connection to remote servers
* `iex`: Same as PowerShell `Invoke-Expression` to execute a command
* `New-Object System.Net.WebClient`: Creates a `WebClient` object
* `DownloadString`: A `WebClient` method to download a script from a string
* `https://community.chocolatey.org/install.ps1`: Script to install Chocolatey. You can open this link to review the code.

Reload profile:

    . $profile

Restart PowerShell.

**For reference: Output after installing choco**

    Forcing web requests to allow TLS v1.2 (Required for requests to Chocolatey.org)
    Getting latest version of the Chocolatey package for download.
    Not using proxy.
    Getting Chocolatey from https://community.chocolatey.org/api/v2/package/chocolatey/1.3.1.
    Downloading https://community.chocolatey.org/api/v2/package/chocolatey/1.3.1 to C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall\chocolatey.zip
    Not using proxy.
    Extracting C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall\chocolatey.zip to C:\Users\...\AppData\Local\Temp\2\chocolatey\chocoInstall
    Installing Chocolatey on the local machine
    Creating ChocolateyInstall as an environment variable (targeting 'Machine')
    Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'
    WARNING: It's very likely you will need to close and reopen your shell
    before you can use choco.
    Restricting write permissions to Administrators
    We are setting up the Chocolatey package repository.
    The packages themselves go to 'C:\ProgramData\chocolatey\lib'
    (i.e. C:\ProgramData\chocolatey\lib\yourPackageName).
    A shim file for the command line goes to 'C:\ProgramData\chocolatey\bin'
    and points to an executable in 'C:\ProgramData\chocolatey\lib\yourPackageName'.

    Creating Chocolatey folders if they do not already exist.

    WARNING: You can safely ignore errors related to missing log files when
    upgrading from a version of Chocolatey less than 0.9.9.
    'Batch file could not be found' is also safe to ignore.
    'The system cannot find the file specified' - also safe.
    chocolatey.nupkg file not installed in lib.
    Attempting to locate it from bootstrapper.
    PATH environment variable does not have C:\ProgramData\chocolatey\bin in it. Adding...
    Chocolatey (choco.exe) is now ready.
    You can call choco from anywhere, command line or powershell by typing choco.
    Run choco /? for a list of functions.
    You may need to shut down and restart powershell and/or consoles
    first prior to using choco.
    Ensuring Chocolatey commands are on the path
    Ensuring chocolatey.nupkg is in the lib folder

## WARNING: Not setting tab completion

See the Choco official docs [here](https://docs.chocolatey.org/en-us/troubleshooting) for troubleshooting.

If you get the warning:

    WARNING: Not setting tab completion: Profile file does not exist at 'C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1'.

Make sure that a `$profile` was created. Then edit `Microsoft.PowerShell_profile.ps1` and add this code:

    # Import the Chocolatey Profile that contains the necessary code to enable
    # tab-completions to function for `choco`.
    # Be aware that if you are missing these lines from your profile, tab completion
    # for `choco` will not function.
    # See https://ch0.co/tab-completion for details.
    $ChocolateyProfile = "$env:ChocolateyInstall\helpers\chocolateyProfile.psm1"
    if (Test-Path($ChocolateyProfile)) {
    Import-Module "$ChocolateyProfile"
    }

## Install Azure Data Studio

Use `--yes` to confirm all prompts including `--accept-license`:

    choco install azure-data-studio --yes

Output:

    Chocolatey v1.3.1
    Installing the following packages:
    azure-data-studio
    By installing, you accept licenses for the packages.
    Progress: Downloading azure-data-studio 1.43.0... 100%

    azure-data-studio v1.43.0 [Approved]
    azure-data-studio package files install completed. Performing other installation steps.
    Merge Tasks:
    !runCode
    Downloading azure-data-studio 64 bit
    from 'https://sqlopsbuilds.azureedge.net/stable/b790d700898b1095d83e62f0de14678a58222520/azuredatastudio-windows-setup-1.43.0.exe'
    Progress: 100% - Completed download of C:\Users\...\AppData\Local\Temp\2\chocolatey\azure-data-studio\1.43.0\azuredatastudio-windows-setup-1.43.0.exe (128.31 MB).
    Download of azuredatastudio-windows-setup-1.43.0.exe (128.31 MB) completed.
    Hashes match.
    Installing azure-data-studio...
    azure-data-studio has been installed.
    azure-data-studio can be automatically uninstalled.
    Environment Vars (like PATH) have changed. Close/reopen your shell to
    see the changes (or in powershell/cmd.exe just type `refreshenv`).
    The install of azure-data-studio was successful.
    Software installed to 'C:\Program Files\Azure Data Studio\'

    Chocolatey installed 1/1 packages.
    See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).

I still don't get why the name `Chocolatey`. It says it's a pun from NuGet. I would have picked the name `loco` as in `loco install`.

## Install SSMS

    choco install sql-server-management-studio --yes

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)