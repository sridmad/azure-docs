---
title: Create and upload a FreeBSD VM image | Microsoft Docs
description: Learn to create and upload a virtual hard disk (VHD) that contains the FreeBSD operating system to create an Azure virtual machine
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: timlt
editor: ''
tags: azure-service-management

ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao

---
# Create and upload a FreeBSD VHD to Azure
This article shows you how to create and upload a virtual hard disk (VHD) that contains the FreeBSD operating system. After you upload it, you can use it as your own image to create a virtual machine (VM) in Azure.

> [!IMPORTANT] 
> Azure has two different deployment models for creating and working with resources: [Resource Manager and Classic](../../../resource-manager-deployment-model.md). This article covers using the Classic deployment model. Microsoft recommends that most new deployments use the Resource Manager model. For information about uploading a VHD using the Resource Manager model, see [here](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## Prerequisites
This article assumes that you have the following items:

* **An Azure subscription**--If you don't have an account, you can create one in just a couple of minutes. If you have an MSDN subscription, see [Monthly Azure credit for Visual Studio subscribers](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Otherwise, learn how to [create a free trial account](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure PowerShell tools**--The Azure PowerShell module must be installed and configured to use your Azure subscription. To download the module, see [Azure downloads](https://azure.microsoft.com/downloads/). A tutorial that describes how to install and configure the module is available here. Use the [Azure Downloads](https://azure.microsoft.com/downloads/) cmdlet to upload the VHD.
* **FreeBSD operating system installed in a .vhd file**--A supported   FreeBSD operating system must be installed to a virtual hard disk. Multiple tools exist to create .vhd files. For example, you can use a virtualization solution such as Hyper-V to create the .vhd file and install the operating system. For instructions about how to install and use Hyper-V, see [Install Hyper-V and create a virtual machine](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> The newer VHDX format is not supported in Azure. You can convert the disk to VHD format by using Hyper-V Manager or the cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). In addition, there is a [tutorial on MSDN about how to use FreeBSD with Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

This task includes the following four steps:

## Step 1: Prepare the image for upload
On the virtual machine where you installed the FreeBSD operating system, complete the following procedures:

1. Enable DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Enable SSH.

    Ensure that the SSH server is installed and configured to start at boot time. By default it is enabled after installation from FreeBSD disc. 
3. Set up a serial console.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Install sudo.

    The root account is disabled in Azure. This means you need to utilize sudo from an unprivileged user to run commands with elevated privileges.

        # pkg install sudo
   
5. Prerequisites for Azure Agent.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Install Azure Agent.

    The latest release of the Azure Agent can always be found on [github](https://github.com/Azure/WALinuxAgent/releases). The version 2.0.10 + officially supports FreeBSD 10 & 10.1, and the version 2.1.4 + (including 2.2.x) officially supports FreeBSD 10.2 and later releases.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    For 2.0, let's use 2.0.16 as an example:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    For 2.1, let's use 2.1.4 as an example:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > After you install Azure Agent, it's a good idea to verify that it's running:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Deprovision the system.

    Deprovision the system to clean it and make it suitable for reprovisioning. The following command also deletes the last provisioned user account and the associated data:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Now you can shut down your VM.

## Step 2: Prepare the connection to Azure
Make sure you are using the Azure CLI in the classic deployment model (`azure config mode asm`), then log in to your account:

```azurecli
azure login
```


<a id="upload"> </a>


## Step 3: Upload the .vhd file

You need a storage account to upload your VHD file to. You can either pick an existing storage account or [create a new one](../../../storage/common/storage-create-storage-account.md).

When you upload the .vhd file, you can place it anywhere within your Blob storage. Following are some terms you will use when you upload the file:

* **BlobStorageURL** is the URL for the storage account that you created in Step 2.
* **YourImagesFolder** is the container within Blob storage where you want to store your images.
* **VHDName** is the label that appears in the Azure classic portal to identify the virtual hard disk.
* **PathToVHDFile** is the full path and name of the .vhd file.

From the Azure PowerShell window you used in the previous step, type:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## Step 4: Create a VM with the uploaded .vhd file
After you upload the .vhd file, you can add it as an image to the list of custom images that are associated with your subscription and create a virtual machine with this custom image.

1. From the Azure PowerShell window you used in the previous step, type:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Use Linux as the OS type. The current Azure PowerShell version accepts only “Linux” or “Windows” as a parameter.
   >
   >
2. After you complete the previous steps, the new image is listed when you choose the **Images** tab on the Azure classic portal.  

    ![Choose an image](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Create a virtual machine from the gallery. This new image is now available under **My Images**.
4. Select the new image. Next, go through the prompts to set up a host name, password, SSH key, and so on.

    ![Custom image](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. After you complete the provisioning, you'll see your FreeBSD VM running in Azure.

    ![FreeBSD image in azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
