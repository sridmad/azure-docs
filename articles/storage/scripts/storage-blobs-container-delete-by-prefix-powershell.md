---
title: Azure PowerShell Script Sample - Delete containers by prefix | Microsoft Docs
description: Delete Azure Storage blob containers based on a container name prefix.
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn

ms.assetid:
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: robinsh
---

# Delete containers based on container name prefix

This script deletes containers in Azure Blob storage based on a prefix in the container name.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Sample script

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## Clean up deployment 

Run the following command to remove the resource group, remaining containers, and all related resources.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## Script explanation

This script uses the following commands to delete containers based on container name prefix. Each item in the table links to command-specific documentation.

| Command | Notes |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Gets a specified Storage account or all of the Storage accounts in a resource group or the subscription. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Lists the storage containers associated with a storage account. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Removes the specified storage container. |

## Next steps

For more information on the Azure PowerShell module, see [Azure PowerShell documentation](/powershell/azure/overview).

Additional storage PowerShell script samples can be found in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md).
