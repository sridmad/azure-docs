---
title: Azure CLI Script Sample - Monitor a web app with web server logs | Microsoft Docs
description: Azure CLI Script Sample - Monitor a web app with web server logs
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management

ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
---

# Monitor a web app with web server logs

In this scenario you will create a resource group, app service plan, web app and configure the web app to enable web server logs. You will then download the log files for review.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Sample script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands to create a resource group, web app, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Creates a resource group in which all resources are stored. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Creates an App Service plan. This is like a server farm for your Azure web app. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Creates an Azure web app. |
| [az webapp log config](https://docs.microsoft.com/cli/azure/webapp/log#az_webapp_log_config) | Configures which logs an Azure web app will persist. |
| [az webapp log download](https://docs.microsoft.com/cli/azure/webapp/log#az_webapp_log_download) | Downloads the logs of the an Azure web app to your local machine. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](../app-service-cli-samples.md).
