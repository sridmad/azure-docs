---
title: Run a custom Docker Hub image in Azure Web App for Containers | Microsoft Docs
description: How to use a custom Docker image for Azure Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''

ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
---

# Run a custom Docker Hub image in Azure Web App for Containers

App Service provides pre-defined application stacks on Linux with support for specific versions, such as PHP 7.0 and Node.js 4.5. You can also use a custom Docker image to run your web app on an application stack that is not already defined in Azure. This quickstart shows how to create a web app and deploy the [official Nginx Docker image](https://hub.docker.com/r/_/nginx/) to it. You create the web app using the [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Sample app running in Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## Create a Web App for Container

Create a [web app](../app-service-web-overview.md) in the `myAppServicePlan` App Service plan with the [az webapp create](/cli/azure/webapp#create) command. Don't forget to replace `<app name>` with a unique app name.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

In the preceding command, `--deployment-container-image-name` points to the public Docker Hub image [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

When the web app has been created, the Azure CLI shows output similar to the following example:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## Browse to the app

Browse to the following URL using your web browser.

```bash
http://<app_name>.azurewebsites.net
```

![Sample app running in Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Congratulations!** You've deployed a custom Docker image to Web App for Containers.

## Next steps

> [!div class="nextstepaction"]
> [Use a custom Docker image](tutorial-custom-docker-image.md)
