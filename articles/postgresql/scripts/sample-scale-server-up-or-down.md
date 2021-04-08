---
title: "Azure CLI Script-Scale Azure Database for PostgreSQL | Microsoft Docs"
description: "Azure CLI Script Sample - Scale Azure Database for PostgreSQL server to a different performance level after querying the metrics."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/03/2017
---
# Monitor and scale a single PostgreSQL server using Azure CLI
This sample CLI script scales a single Azure Database for PostgreSQL server to a different performance level after querying the metrics. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this article requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Sample script
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the az monitor commands with your own subscription identifier.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## Clean up deployment
After the script sample has been run, the following command can be used to remove the resource group and all resources associated with it.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## Script explanation
This script uses the following commands. Each command in the table links to command specific documentation.

| **Command** | **Notes** |
|---|---|
| [az group create](/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az postgres server create](/cli/azure/postgres/server#create) | Creates a PostgreSQL server that hosts the databases. |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | List the metric value for the resources. |
| [az group delete](/cli/azure/group#delete) | Deletes a resource group including all nested resources. |

## Next steps
- Read more information on the Azure CLI: [Azure CLI documentation](/cli/azure/overview)
- Try additional scripts: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Read more information on scaling: [Service Tiers](../concepts-service-tiers.md) and [Compute Units and Storage Units](../concepts-compute-unit-and-storage.md)
