---
title: Get started with Storage Explorer (Preview) | Microsoft Docs
description: Manage Azure storage resources with Storage Explorer (Preview)
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''

ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa

---
# Get started with Storage Explorer (Preview)
## Overview
Azure Storage Explorer (Preview) is a standalone app that enables you to easily work with Azure Storage data on Windows, macOS, and Linux. In this article, you learn the various ways of connecting to and managing your Azure storage accounts.

![Microsoft Azure Storage Explorer (Preview)][15]

## Prerequisites
* [Download and install Storage Explorer (Preview)](http://www.storageexplorer.com)

## Connect to a storage account or service
Storage Explorer (Preview) provides several ways to connect to storage accounts. For example, you can:
* Connect to storage accounts associated with your Azure subscriptions.
* Connect to storage accounts and services that are shared from other Azure subscriptions.
* Connect to and manage local storage by using the Azure Storage Emulator. 

In addition, you can work with storage accounts in global and national Azure:

* [Connect to an Azure subscription](#connect-to-an-azure-subscription): Manage storage resources that belong to your Azure subscription.
* [Work with local development storage](#work-with-local-development-storage): Manage local storage by using the Azure Storage Emulator.
* [Attach to external storage](#attach-or-detach-an-external-storage-account): Manage storage resources that belong to another Azure subscription or that are under national Azure clouds by using the storage account's name, key, and endpoints.
* [Attach a storage account by using an SAS](#attach-storage-account-using-sas): Manage storage resources that belong to another Azure subscription by using a shared access signature (SAS).
* [Attach a service by using an SAS](#attach-service-using-sas): Manage a specific storage service (blob container, queue, or table) that belongs to another Azure subscription by using an SAS.
* [Connect to an Azure Cosmos DB account by using a connection string](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Manage Cosmos DB account by using a connection string.

## Connect to an Azure subscription
> [!NOTE]
> If you don't have an Azure account, you can [sign up for a free trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) or [activate your Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. In Storage Explorer (Preview), select **Azure Account settings**.

    ![Azure account settings][0]

2. The left pane displays all the Microsoft accounts you've signed in to. To connect to another account, select **Add an account**, and then follow the instructions to sign in with a Microsoft account that is associated with at least one active Azure subscription.

    >[!NOTE]
    >Connecting to national Azure (such as Azure Germany, Azure Government, and Azure China via sign-in) is currently not supported. See the "Attach or detach an external storage account" section for how to connect to national Azure storage accounts.

3. After you successfully sign in with a Microsoft account, the left pane is populated with the Azure subscriptions associated with that account. Select the Azure subscriptions that you want to work with, and then select **Apply**. (Selecting **All subscriptions** toggles selecting all or none of the listed Azure subscriptions.)

    ![Select Azure subscriptions][3]  
    The left pane displays the storage accounts associated with the selected Azure subscriptions.

    ![Selected Azure subscriptions][4]

## Connect to an Azure Stack subscription

For information about connecting to an Azure Stack subscription, see [Connect Storage Explorer to an Azure Stack subscription](azure-stack/user/azure-stack-storage-connect-se.md).

## Work with local development storage
With Storage Explorer (Preview), you can work against local storage by using the Azure Storage Emulator. This approach lets you write code against and test storage without necessarily having a storage account deployed on Azure, because the storage account is being emulated by the Azure Storage Emulator.

> [!NOTE]
> The Azure Storage Emulator is currently supported only for Windows.
>
>

1. In the left pane of Storage Explorer (Preview), expand the **(Local and Attached)** > **Storage Accounts** > **(Development)** node.

    ![Local development node][21]

2. If you have not yet installed the Azure Storage Emulator, you are prompted to do so via an infobar. If the infobar is displayed, select **Download the latest version**, and then install the emulator.

    ![Download Azure Storage Emulator prompt][22]

3. After the emulator is installed, you can create and work with local blobs, queues, and tables. To learn how to work with each storage account type, see one of the following:

    * [Manage Azure blob storage resources](vs-azure-tools-storage-explorer-blobs.md)
    * Manage Azure file share storage resources: *Coming soon*
    * Manage Azure queue storage resources: *Coming soon*
    * Manage Azure table storage resources: *Coming soon*

## Attach or detach an external storage account
With Storage Explorer (Preview), you can attach to external storage accounts so that storage accounts can be easily shared. This section explains how to attach to (and detach from) external storage accounts.

### Get the storage account credentials
To share an external storage account, the owner of that account must first get the credentials (account name and key) for the account and then share that information with the person who wants to attach to that (external) account. You can obtain the storage account credentials via the Azure portal by doing the following:

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Select **Browse**.

3. Select **Storage Accounts**.

4. On the **Storage Accounts** blade, select the desired storage account.

5. On the **Settings** blade for the selected storage account, select **Access keys**.

    ![Access Keys option][5]

6. On the **Access keys** blade, copy the **Storage account name** and **key1** values for use when attaching to the storage account.

    ![Access keys][6]

### Attach to an external storage account
To attach to an external storage account, you need the account's name and key. The "Get the storage account credentials" section explains how to obtain these values from the Azure portal. However, in the portal, the account key is called **key1**. So where Storage Explorer (Preview) asks for an account key, you enter the **key1** value.

1. In Storage Explorer (Preview), select **Connect to Azure storage**.

    ![Connect to Azure storage option][23]

2. In the **Connect to Azure Storage** dialog box, specify the account key (the **key1** value from the Azure portal), and then select **Next**.

    > [!NOTE]
    > You can enter the storage connection string from a storage account on national Azure. For example, to connect to Azure Germany storage accounts, enter connection strings similar to the following: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >You can get the connection string from the Azure portal in the same way as described in the "Get the storage account credentials" section.

    ![Connect to Azure storage dialog box][24]

3. In the **Attach External Storage** dialog box, in the **Account name** box, enter the storage account name, specify any other desired settings, and then select **Next**.

    ![Attach external storage dialog box][8]

4. In the **Connection Summary** dialog box, verify the information. If you want to change anything, select **Back** and reenter the desired settings. 

5. Select **Connect**.

6. After it is successfully connected, the external storage account is displayed with **(External)** appended to the storage account name.

    ![Result of connecting to an external storage account][9]

### Detach from an external storage account
1. Right-click the external storage account that you want to detach, and then select **Detach**.

    ![Detach from storage option][10]

2. In the confirmation message, select **Yes** to confirm the detachment from the external storage account.

## Attach a storage account by using an SAS
An [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) lets the admin of an Azure subscription grant temporary access to a storage account without having to provide Azure subscription credentials.

To illustrate this scenario, let's say that UserA is an admin of an Azure subscription, and UserA wants to allow UserB to access a storage account for a limited time with certain permissions:

1. UserA generates an SAS (consisting of the connection string for the storage account) for a specific time period and with the desired permissions.

2. UserA shares the SAS with the person (UserB, in our example) who wants access to the storage account.  

3. UserB uses Storage Explorer (Preview) to attach to the account that belongs to UserA by using the supplied SAS.

### Get an SAS for the account you want to share
1. In Storage Explorer (Preview), right-click the storage account you want share, and then select **Get Shared Access Signature**.

    ![Get SAS context menu option][13]

2. In the **Shared Access Signature** dialog box, specify the time frame and permissions that you want for the account, and then select **Create**.

    ![Get SAS dialog box][14]  
    The **Shared Access Signature** dialog box opens and displays the SAS.

3. Next to the **Connection String**, select **Copy** to copy it to the clipboard, and then select **Close**.

### Attach to the shared account by using the SAS
1. In Storage Explorer (Preview), select **Connect to Azure storage**.

    ![Connect to Azure storage option][23]

2. In the **Connect to Azure Storage** dialog box, specify the connection string, and then select **Next**.

    ![Connect to Azure storage dialog box][24]

3. In the **Connection Summary** dialog box, verify the information. To make changes, select **Back**, and then enter the settings you want. 

4. Select **Connect**.

5. After it is attached, the storage account is displayed with **(SAS)** appended to the account name that you supplied.

    ![Result of attached to an account by using SAS][17]

## Attach a service by using an SAS
The "Attach a storage account by using an SAS" section explains how an Azure subscription admin can grant temporary access to a storage account by generating and sharing an SAS for the storage account. Similarly, an SAS can be generated for a specific service (blob container, queue, or table) within a storage account.  

### Generate an SAS for the service that you want to share
In this context, a service can be a blob container, queue, or table. To generate the SAS for a listed service, see:

* [Get the SAS for a blob container](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Get the SAS for a file share: *Coming soon*
* Get the SAS for a queue: *Coming soon*
* Get the SAS for a table: *Coming soon*

### Attach to the shared account service by using the SAS
1. In Storage Explorer (Preview), select **Connect to Azure storage**.

    ![Connect to Azure storage option][23]

2. In the **Connect to Azure Storage** dialog box, specify the SAS URI, and then select **Next**.

    ![Connect to Azure storage dialog box][24]

3. In the **Connection Summary** dialog box, verify the information. To make changes, select **Back**, and then enter the settings you want. 

4. Select **Connect**.

5. After it is attached, the newly attached service is displayed under the **(Service SAS)** node.

    ![Result of attaching to a shared service by using an SAS][20]

## Connect to an Azure Cosmos DB account by using a connection string
Besides manage Azure Cosmos DB accounts through Azure subscription, an alternative way of connecting to an Azure Cosmos DB is to use a connection string. Use the following steps to connect using a connection string.

1. Find **Local and Attached** in the left tree, right-click **Azure Cosmos DB Accounts**, choose **Connect to Azure Cosmos DB...**

    ![connect to Azure Cosmos DB by connection string][33]

2. Choose Azure Cosmos DB API, paste your **Connection String**, and then click **OK** to connect Azure Cosmos DB account. For information on retrieving the connection string, see [Get the connection string](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## Search for storage accounts
If you have a long list of storage accounts, a quick way to locate a particular storage account is to use the search box at the top of the left pane.

As you type in the search box, the left pane displays the storage accounts that match the search value you've entered up to that point. For example, a search for all storage accounts whose name contains **tarcher** is shown in the following screenshot:

![Storage account search][11]

## Next steps
* [Manage Azure Blob Storage resources with Storage Explorer (Preview)](vs-azure-tools-storage-explorer-blobs.md)
* [Manage Azure Cosmos DB in Azure Storage Explorer  (Preview)](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
