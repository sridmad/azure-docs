---
title: Use Azure Files with Linux | Microsoft Docs
description: Learn how to mount an Azure File share over SMB on Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn

ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
---
# Use Azure Files with Linux
[Azure Files](storage-files-introduction.md) is Microsoft's easy to use cloud file system. Azure File shares can be mounted in Linux distributions using the [cifs-utils package](https://wiki.samba.org/index.php/LinuxCIFS_utils) from the [Samba project](https://www.samba.org/). This article shows two ways to mount an Azure File share: on-demand with the `mount` command and on-boot by creating an entry in `/etc/fstab`.

> [!NOTE]  
> In order to mount an Azure File share outside of the Azure region it is hosted in, such as on-premises or in a different Azure region, the OS must support the encryption functionality of SMB 3.0. Encryption feature for SMB 3.0 for Linux was introduced in 4.11 kernel. This feature enables mounting of Azure File share from on-premises or a different Azure region. At the time of publishing, this functionality has been backported to Ubuntu from 16.04 and above.


## Prerequisities for mounting an Azure File share with Linux and the cifs-utils package
* **Pick a Linux distribution that can have the cifs-utils package installed**: Microsoft recommends the following Linux distributions in the Azure image gallery:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**The cifs-utils package is installed**: The cifs-utils can be installed using the package manager on the Linux distribution of your choice. 

    On **Ubuntu** and **Debian-based** distributions, use the `apt-get` package manager:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    On **RHEL** and **CentOS**, use the `yum` package manager:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    On **openSUSE**, use the `zypper` package manager:

    ```
    sudo zypper install samba*
    ```

    On other distributions, use the appropriate package manager or [compile from source](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Decide on the directory/file permissions of the mounted share**: In the examples below, we use 0777, to give read, write, and execute permissions to all users. You can replace it with other [chmod permissions](https://en.wikipedia.org/wiki/Chmod) as desired. 

* **Storage Account Name**: To mount an Azure File share, you will need the name of the storage account.

* **Storage Account Key**: To mount an Azure File share, you will need the primary (or secondary) storage key. SAS keys are not currently supported for mounting.

* **Ensure port 445 is open**: SMB communicates over TCP port 445 - check to see if your firewall is not blocking TCP ports 445 from client machine.

## Mount the Azure File share on-demand with `mount`
1. **[Install the cifs-utils package for your Linux distribution](#install-cifs-utils)**.

2. **Create a folder for the mount point**: This can be done anywhere on the file system.

    ```
    mkdir mymountpoint
    ```

3. **Use the mount command to mount the Azure File share**: Remember to replace `<storage-account-name>`, `<share-name>`, and `<storage-account-key>` with the proper information.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> When you are done using the Azure File share, you may use `sudo umount ./mymountpoint` to unmount the share.

## Create a persistent mount point for the Azure File share with `/etc/fstab`
1. **[Install the cifs-utils package for your Linux distribution](#install-cifs-utils)**.

2. **Create a folder for the mount point**: This can be done anywhere on the file system, but you need to note the absolute path of the folder. The following example creates a folder under root.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Use the following command to append the following line to `/etc/fstab`**: Remember to replace `<storage-account-name>`, `<share-name>`, and `<storage-account-key>` with the proper information.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> You can use `sudo mount -a` to mount the Azure File share after editing `/etc/fstab` instead of rebooting.

## Feedback
Linux users, we want to hear from you!

The Azure Files for Linux users' group provides a forum for you to share feedback as you evaluate and adopt File storage on Linux. Email [Azure Files Linux Users](mailto:azurefileslinuxusers@microsoft.com) to join the users' group.

## Next steps
See these links for more information about Azure Files.
* [File Service REST API reference](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [How to use AzCopy with Microsoft Azure storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [FAQ](../storage-files-faq.md)
* [Troubleshooting](storage-troubleshoot-linux-file-connection-problems.md)
