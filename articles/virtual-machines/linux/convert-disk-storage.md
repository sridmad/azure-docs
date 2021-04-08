---
title: Convert Azure managed disks storage from standard to premium, and vice versa | Microsoft Docs
description: How to convert Azure managed disks storage from standard to premium, and vice versa, by using Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
---

# Convert Azure managed disks storage from standard to premium, and vice versa

Managed disks offers two storage options: [Premium](../windows/premium-storage.md) (SSD-based) and [Standard](../windows/standard-storage.md) (HDD-based). It allows you to easily switch between the two options with minimal downtime based on your performance needs. This capability is not available for unmanaged disks. But you can easily [convert to managed disks](convert-unmanaged-to-managed-disks.md) to easily switch between the two options.

This article shows you how to convert managed disks from standard to premium, and vice versa by using Azure CLI. If you need to install or upgrade it, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## Before you begin

* The conversion requires a restart of the VM, so schedule the migration of your disks storage during a pre-existing maintenance window. 
* If you are using unmanaged disks, first [convert to managed disks](convert-unmanaged-to-managed-disks.md) to use this article to switch between the two storage options. 


## Convert all the managed disks of a VM from standard to premium, and vice versa

In the following example, we show how to switch all the disks of a VM from standard to premium storage. To use premium managed disks, your VM must use a [VM size](sizes.md) that supports premium storage. This example also switches to a size that supports premium storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## Convert a managed disk from standard to premium, and vice versa

For your dev/test workload, you may want to have mixture of standard and premium disks to reduce your cost. You can accomplish it by upgrading to premium storage, only the disks that require better performance. In the following example, we show how to switch a single disk of a VM from standard to premium storage, and vice versa. To use premium managed disks, your VM must use a [VM size](sizes.md) that supports premium storage. This example also switches to a size that supports premium storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## Next steps

Take a read-only copy of a VM by using [snapshots](snapshot-copy-managed-disk.md).

