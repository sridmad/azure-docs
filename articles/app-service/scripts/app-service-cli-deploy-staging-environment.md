---
title: Azure CLI Script Sample - Create a web app and deploy code to a staging environment | Microsoft Docs
description: Azure CLI Script Sample - Create a web app and deploy code to a staging environment
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management

ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
---

# Create a web app and deploy code to a staging environment

This sample script creates a web app in App Service with an additional deployment slot called "staging", and then deploys a sample app to the "staging" slot.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Sample script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create a web app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Creates a resource group in which all resources are stored. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Creates an App Service plan. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Creates an Azure web app. |
| [az webapp deployment slot create](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_create) | Create a deployment slot. |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Associates an Azure web app with a Git or Mercurial repository. |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Open an Azure web app in a browser. |
| [az webapp deployment slot swap](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_swap) | Swap a specified deployment slot into production. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](../app-service-cli-samples.md).
