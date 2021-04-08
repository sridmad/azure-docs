---
title: Azure Government Storage | Microsoft Docs
description: This provides a quickstart-guide for getting started with Storage in Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: yujhongmicrosoft
manager: zakramer

ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 9/08/2017
ms.author: yujhong

---
# Connect to Storage in Azure Government
Azure Government uses the same underlying technologies as commercial Azure, enabling you to use the development tools you’re already familiar with.
In order to use these services in Azure Government, you must define different endpoint mappings, as shown below for the Storage service. 

## Connecting Storage Explorer to Azure Government
[The Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) is a cross-platform tool for working with Azure Storage. Government customers will now be able to take advantage of all the latest features of the Azure Storage Explorer such as being able to create and manage blobs, queues, tables, and file shares.
### Prerequisites
* Download and install the latest version of Azure Storage Explorer [here](https://azure.microsoft.com/features/storage-explorer/). 
* Have an active Azure Government subscription.
If you don't have an Azure Government subscription, create a [free account](https://azure.microsoft.com/overview/clouds/government/) before you begin.

### Getting Started with Storage Explorer
1. Open the Azure Storage Explorer desktop application.

2. You will be prompted to add an Azure account; in the dropdown choose the “Azure US Government” option:

    ![storage1](./media/documentation-government-get-started-connect-with-storage-img1.png)
3. Log in to your Azure Government account and you will be able to see all of your resources. The Storage Explorer should look similar to the screenshot below. Click on your Storage Account to see the blob containers, file shares, Queues, and Tables. 

    ![storage2](./media/documentation-government-get-started-connect-with-storage-img2.png)

For more information on Azure Storage Explorer, click [here](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## Connecting to the Storage API 

### Prerequisites
* Have an active Azure Government subscription.
If you don't have an Azure Government subscription, create a [free account](https://azure.microsoft.com/overview/clouds/government/) before you begin.
* Download Visual Studio 2017 and [Connect to Azure Government](documentation-government-get-started-connect-with-vs.md).

### Getting Started with Storage API
One important difference to note when connecting with the Storage API is that the URL for storage is different than the URL for storage in commercial Azure – specifically, the domain ends with “core.usgovcloudapi.net”, rather than “core.windows.net”.

These endpoint differences must be taken into account when you connect to storage in Azure Government with C#.
1. Go to the [Azure Government portal](https://portal.azure.us) and select your storage account and then click the “Access Keys” tab:

    ![storage4](./media/documentation-government-get-started-connect-with-storage-img4.png)
2. Copy/paste the storage account name and key.
3. Open up Visual Studio and create a new project. Add a reference to the [WindowsAzure.Storage NuGet package](https://www.nuget.org/packages/WindowsAzure.Storage/). This NuGet package contains classes we will need to connect to your storage account.

4. Add these two lines of C# code to connect:
	```cs
	var credentials = new StorageCredentials(storageAccountName, storageAccountKey);

    var storageAccount = new CloudStorageAccount(credentials, "core.usgovcloudapi.net", useHttps: true);   
	```

    -   Notice on the second line we had to use a [particular constructor for the CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.-ctor?view=azure-dotnet) – enabling us to explicitly pass in the endpoint suffix of “core.usgovcloudapi.net”. This constructor is the **only difference** your code requires to connect to storage in Azure Government as compared with commercial Azure.

5. At this point, we can interact with storage as we normally would. For example, if we want to retrieve a specific record from our table storage we could do it like this:

   ```cs
    var tableClient = storageAccount.CreateCloudTableClient();

    var table = tableClient.GetTableReference("Contacts");
    var retrieveOperation = TableOperation.Retrieve<ContactEntity>("gov-partition1", "0fb52a6c-3784-4dc5-aa6d-ecda4426dbda");
    var result = await table.ExecuteAsync(retrieveOperation);
    var contact = result.Result as ContactEntity;
    Console.WriteLine($"Contact: {contact.FirstName} {contact.LastName}");
    ```

## Next Steps
* Read more about [Azure Storage](https://docs.microsoft.com/azure/storage/). 
* Subscribe to the [Azure Government blog](https://blogs.msdn.microsoft.com/azuregov/)
* Get help on Stack Overflow by using the "[azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)" tag
* Give us feedback or request new features via the [Azure Government feedback forum](https://feedback.azure.com/forums/558487-azure-government)
