---
title: Create an Azure Function that connects to an Azure Storage | Microsoft Docs
description: Azure CLI Script Sample - Create an Azure Function that connects to an Azure Storage
services: functions
documentationcenter: functions
author: rachelappel
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
---
# Integrate Function App into Azure Storage Account

This sample script creates a Function App and Storage Account.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Sample script

This sample creates an Azure Function app and adds the storage connection string to an app setting.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## Clean up deployment

After the script sample has been run, the following command can be used to remove the resource group, App Service app, and all related resources:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Login to Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Create a resource group with location |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Create a storage account |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Create a new function app |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Clean up |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Functions CLI script samples can be found in the [Azure Functions documentation](../functions-cli-samples.md).
