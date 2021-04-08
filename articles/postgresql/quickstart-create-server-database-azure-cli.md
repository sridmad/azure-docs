---
title: 'Create an Azure Database for PostgreSQL using the Azure CLI | Microsoft Docs'
description: 'Quickstart guide to create and manage Azure Database for PostgreSQL server using Azure CLI (command line interface).'
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 11/03/2017
---
# Create an Azure Database for PostgreSQL using the Azure CLI
Azure Database for PostgreSQL is a managed service that enables you to run, manage, and scale highly available PostgreSQL databases in the cloud. The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This quickstart shows you how to create an Azure Database for PostgreSQL server in an [Azure resource group](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) using the Azure CLI.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this article requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

If you are running the CLI locally, you need to log in to your account using the [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) command.
```azurecli-interactive
az login
```

If you have multiple subscriptions, choose the appropriate subscription in which the resource should be billed. Select a specific subscription ID under your account using [az account set](/cli/azure/account#set) command.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## Create a resource group

Create an [Azure resource group](../azure-resource-manager/resource-group-overview.md) using the [az group create](/cli/azure/group#create) command. A resource group is a logical container into which Azure resources are deployed and managed as a group. The following example creates a resource group named `myresourcegroup` in the `westus` location.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## Create an Azure Database for PostgreSQL server

Create an [Azure Database for PostgreSQL server](overview.md) using the [az postgres server create](/cli/azure/postgres/server#create) command. A server contains a group of databases managed as a group. 

The following example creates a server named `mypgserver-20170401` in your resource group `myresourcegroup` with server admin login `mylogin`. The name of a server maps to DNS name and is thus required to be globally unique in Azure. Substitute the `<server_admin_password>` with your own value.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> The server admin login and password that you specify here are required to log in to the server and its databases later in this quick start. Remember or record this information for later use.

By default, **postgres** database gets created under your server. The [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) database is a default database meant for use by users, utilities, and third-party applications. 


## Configure a server-level firewall rule

Create an Azure PostgreSQL server-level firewall rule with the [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) command. A server-level firewall rule allows an external application, such as [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) or [PgAdmin](https://www.pgadmin.org/) to connect to your server through the Azure PostgreSQL service firewall. 

You can set a firewall rule that covers an IP range to be able to connect from your network. The following example uses [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) to create a firewall rule `AllowAllIps` for an IP address range. To open all IP addresses, use 0.0.0.0 as the starting IP address and 255.255.255.255 as the ending address.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL server communicates over port 5432. When connecting from within a corporate network, outbound traffic over port 5432 may not be allowed by your network's firewall. Have your IT department open port 5432 to connect to your Azure SQL Database server.

## Get the connection information

To connect to your server, you need to provide host information and access credentials.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

The result is in JSON format. Make a note of the **administratorLogin** and **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## Connect to PostgreSQL database using psql

If your client computer has PostgreSQL installed, you can use a local instance of [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) to connect to an Azure PostgreSQL server. Let's now use the psql command-line utility to connect to the Azure PostgreSQL server.

1. Run the following psql command to connect to an Azure Database for PostgreSQL server
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  For example, the following command connects to the default database called **postgres** on your PostgreSQL server **mypgserver-20170401.postgres.database.azure.com** using access credentials. Enter the `<server_admin_password>` you chose when prompted for password.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Once you are connected to the server, create a blank database at the prompt.
```sql
CREATE DATABASE mypgsqldb;
```

3.  At the prompt, execute the following command to switch connection to the newly created database **mypgsqldb**:
```sql
\c mypgsqldb
```

## Connect to PostgreSQL database using pgAdmin

To connect to Azure PostgreSQL server using the GUI tool _pgAdmin_
1.	Launch the _pgAdmin_ application on your client computer. You can install _pgAdmin_ from http://www.pgadmin.org/.
2.	Choose **Add New Server** from the **Quick Links** menu.
3.	In the **Create - Server** dialog box **General** tab, enter a unique friendly Name for the server. Say **Azure PostgreSQL Server**.
 ![pgAdmin tool - create - server](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.	In the **Create - Server** dialog box, **Connection** tab:
    - Enter the fully qualified server name (for example, **mypgserver-20170401.postgres.database.azure.com**) in the **Host Name/ Address** box. 
    - Enter port 5432 into the **Port** box. 
    - Enter the **Server admin login (user@mypgserver)** obtained earlier in this quickstart and password you entered when you created the server into the **Username** and **Password** boxes, respectively.
    - Select **SSL Mode** as **Require**. By default, all Azure PostgreSQL servers are created with SSL enforcing turned ON. To turn OFF SSL enforcing, see details in [Enforcing SSL](./concepts-ssl-connection-security.md).

    ![pgAdmin - Create - Server](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.	Click **Save**.
6.	In the Browser left pane, expand the **Server Groups**. Choose your server **Azure PostgreSQL Server**.
7.  Choose the **Server** you connected to, and then choose **Databases** under it. 
8.	Right-click on **Databases** to Create a Database.
9.	Choose a database name **mypgsqldb** and the owner for it as server admin login **mylogin**.
10. Click **Save** to create a blank database.
11. In the **Browser**, expand the **Servers** group. Expand the server you created, and see the database **mypgsqldb** under it.
 ![pgAdmin - Create - Database](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## Clean up resources

Clean up all resources you created in the quickstart by deleting the [Azure resource group](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Other quickstarts in this collection build upon this quickstart. If you plan to continue to work with subsequent quickstarts, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart in the Azure CLI.

```azurecli-interactive
az group delete --name myresourcegroup
```

If you would just like to delete the one newly created server, you can run [az postgres server delete](/cli/azure/postgres/server#delete) command.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./howto-migrate-using-export-and-import.md)
