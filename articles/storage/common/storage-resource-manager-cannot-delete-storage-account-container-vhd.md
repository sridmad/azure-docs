---
title: Troubleshoot errors when you delete Azure storage accounts, containers, or VHDs | Microsoft Docs
description: Troubleshoot errors when you delete Azure storage accounts, containers, or VHDs
services: storage
documentationcenter: ''
author: passaree
manager: cshepard
editor: na
tags: storage

ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli

---
# Troubleshoot storage delete errors in Resource Manager deployment
This article provides troubleshooting guidance when one of the following errors occur when trying to delete Azure storage account, container, or blob in Azure Resource Manager deployment.

>**Failed to delete storage account 'StorageAccountName'. Error: The storage account cannot be deleted due to its artifacts being in use.**

>**Failed to delete # out of # container(s):<br>vhds: There is currently a lease on the container and no lease ID was specified in the request.**

>**Failed to delete # out of # blobs:<br>BlobName.vhd: There is currently a lease on the blob and no lease ID was specified in the request.**

The Virtual Hard Disks used in Azure VMs are .vhd files stored as page blobs in a standard or premium storage account in Azure.  More information about Azure disks can be found [here](../../virtual-machines/windows/about-disks-and-vhds.md). Azure prevents deletion of a disk that is attached to a VM to prevent corruption. It also prevents deletion of containers and storage accounts which have a page blob that is attached to a VM. 

## Solution
The process to delete a storage account, container, or blob when receiving one of the above errors is: 
1. [Identify blobs attached to a VM](#step-1-identify-blobs-attached-to-a-vm)
2. [Delete VMs with attached **OS disk**](#step-2-delete-vm-to-detach-os-disk)
3. [Detach all **data disk(s)** from remaining VM(s)](#step-3-detach-data-disk-from-the-vm)

Retry deleting storage account, container, or blob after the above steps are completed.

### Step 1: Identify blob attached to a VM

#### Scenario 1: Deleting a blob – identify attached VM
1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the Hub menu, select **All resources**. Go to the storage account, under **Blob Service** select **Containers** and navigate to the blob to be deleted.
3. If the blob **Lease State** is **Leased**, right click, and select **Edit Metadata** to open Blob metadata pane. 

    ![Screenshot of the portal, with the Storage account blobs and right click > "Edit Metadata" hilighted](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. In Blob metadata pane, check and record the value for **MicrosoftAzureCompute_VMName**. This value is the name of the VM that the VHD is attached to. (See **important** if this field does not exist)
5. In Blob metadata pane, check and record the value of **MicrosoftAzureCompute_DiskType**. This identifies if the attached disk is OS or data disk (See **important** if this field does not exist). 

     ![Screenshot of the portal, with the storage "Blob Metadata" pane open](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. If the blob disk type is **OSDisk** follow [Step 2: Delete VM to detach OS disk](#step-2-delete-vm-to-detach-os-disk). Otherwise, if the blob disk type is **DataDisk** follow the steps in [Step 3: Detach data disk from the VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> If **MicrosoftAzureCompute_VMName** and **MicrosoftAzureCompute_DiskType** do not appear in the blob metadata, it indicates that the blob is explicitly leased and is not attached to a VM. Leased blobs cannot be deleted without breaking the lease first. To break lease, right click on the blob and select **Break lease**. Leased blobs which are not attached to a VM prevent deletion of the blob but do not prevent deletion of container or storage account.

#### Scenario 2: Deleting a container - identify all blob(s) within container that are attached to VMs
1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the Hub menu, select **All resources**. Go to the storage account, under **Blob Service** select **Containers** and find the container to be deleted.
3. Click to open the container and the list of blobs inside it will appear. Identify all the blobs with Blob Type = **Page blob** and Lease State = **Leased** from this list. Follow [Scenario 1](#step-1-identify-blobs-attached-to-a-vm) to identify the VM associated with each of these blobs.

    ![Screenshot of the portal, with the Storage account blobs and the "Lease State" with "Leased" highlighted](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Follow [Step 2](#step-2-delete-vm-to-detach-os-disk) and [Step 3](#step-3-detach-data-disk-from-the-vm) to delete VM(s) with **OSDisk** and detach **DataDisk**. 

#### Scenario 3: Deleting storage account - identify all blob(s) within storage account that are attached to VMs
1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the Hub menu, select **All resources**. Go to the storage account, under **Blob Service** select **Containers**.

    ![Screenshot of the portal, with the storage account containers and the "Lease State" with "Leased" highlighted](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. In **Containers** blade, identify all containers where **Lease State** is **Leased** and follow [Scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) for each **Leased** container.
4. Follow [Step 2](#step-2-delete-vm-to-detach-os-disk) and [Step 3](#step-3-detach-data-disk-from-the-vm) to delete VM(s) with **OSDisk** and detach **DataDisk**. 

### Step 2: Delete VM to detach OS disk
If the VHD is an OS disk, you must delete the VM before the attached VHD can be deleted. No additional action will be required for data disks attached to the same VM once these steps are completed:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the Hub menu, select **Virtual Machines**.
3. Select the VM that the VHD is attached to.
4. Make sure that nothing is actively using the virtual machine, and that you no longer need the virtual machine.
5. At the top of the **Virtual Machine details** blade, select **Delete**, and then click **Yes** to confirm.
6. The VM should be deleted, but the VHD can be retained. However, the VHD should no longer be attached to a VM or have a lease on it. It may take a few minutes for the lease to be released. To verify that the lease is released, browse to the blob location and in the **Blob properties** pane, the **Lease Status** should be **Available**.

### Step 3: Detach data disk from the VM
If the VHD is a data disk, detach the VHD from the VM to remove the lease:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the Hub menu, select **Virtual Machines**.
3. Select the VM that the VHD is attached to.
4. Select **Disks** on the **Virtual Machine details** blade.
5. Select the data disk to be deleted the VHD is attached to. You can determine which blob is attached in the disk by checking the URL of the VHD.
6. You can verify the blob location by clicking on the disk to check the path in **VHD URI** field.
7. Select **Edit** on the top of **Disks** blade.
8. Click **detach icon** of the data disk to be deleted.

     ![Screenshot of the portal, with the storage "Blob Metadata" pane open](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Select **Save**. The disk should now be detached from the VM, and the VHD should no longer have a lease on it. It may take a few minutes for the lease to be released. To verify that the lease has been released, browse to the blob location and in the **Blob properties** pane, the **Lease Status** value should be **Unlocked** or **Available**.

## Next steps
Retry deletion of storage object that had previously failed.

