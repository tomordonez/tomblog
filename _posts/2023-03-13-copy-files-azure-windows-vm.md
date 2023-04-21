---
layout: post
title: "Copy Files to an Azure Windows VM"
author: tom
tags: [azure, azure windows vm]
comments: true
---

I am having some issues with `scp` to copy files from Linux to an Azure Windows 10 VM. Here is a procedure to create a shared folder between Azure Storage and a Windows VM.

![Azure Windows VM File Share](/images/azure-windows-vm-file-share.png)

## Create a File Share in Azure Storage

1. Go to Azure Portal
2. Storage account
3. Left menu Data Storage, File Shares
4. Create a File Share
5. Enter a name for it, and select Tier `Transaction optimized`
6. Click Create

For reference. There is a note that says `To use the SMB protocol with this share, check if you can communicate over port 445`

## Get a PowerShell Script to Check Port 445

![Azure Windows VM File Share Script](/images/azure-windows-vm-file-share-connect-smb-script.png)

Another note:

`This script will check to see if this storage account is accessible via TCP port 445, which is the port SMB uses. If port 445 is available, your Azure file share will be persistently mounted.`

1. Open the file share
2. Upload a file
3. Go to `Browse` to see the uploaded file
4. Top menu click `Connect`
5. Windows tab
6. Drive letter leave default `Z`
7. Authentication method `Storage account key`
8. Click `Show script`
9. Copy the script

Warning note:

`Connecting to a share using the storage account key is only appropriate for admin access. Mounting the Azure file share with the Active Directory identity of the user is preferred. `

## Run the script in the Azure VM

![Azure Windows VM File Share PowerShell Script](/images/azure-windows-vm-file-share-runpowershellscript.png)

1. In Azure portal, go to the VM, under Operations
2. Click `Run Command`, then `RunPowerShellScript`.
3. Paste script in this console and `Run`

Top message says `Script execution in progress...`, wait a few minutes, then it should say `Script execution complete`.

The output says `CMDKEY: Credential added successfully`

## Mount the File Share in the Windows VM

![Azure Windows VM File Share Map Network Drive](/images/azure-windows-vm-file-share-map-network-drive.png)

1. Login RDP to the Windows VM
2. Open File Explorer, then `This PC`, top menu `Computer` then `Map network drive`, select `Map network drive`
3. Enter a Drive letter
4. Enter folder using this format example `\\storageaccountname.file.core.windows.net\filesharename`
5. Select both `Reconnect at sign-in` and `Connect using different credentials`
6. For `email address` enter the storage account name
7. For password enter a storage account key (from Azure Portal, Storage account)
8. The file you uploaded through Azure portal in File Share should appear there.

## Connect the File Share in the Windows VM After Restart

When you start the VM again, the network drive will appear as disconnected.

1. Double click on it
2. Enter the same password above (Azure Storage Account key)

## Troubleshooting

If you have the Windows VM running, while you do the procedure, the Network Drive `Z` will show up with an `X` (disconnected). When you mount the File Share, you will have to use a different letter and continue the steps above.

More details

* Azure docs [here](https://learn.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-windows) and [here](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-quick-create-use-windows)