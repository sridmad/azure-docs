---
title: Add messages to an Azure Storage queue using Functions | Microsoft Docs
description: Use Azure Functions to create a serverless function that is invoked by a messages submitted to an Azure Storage queue.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''

ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
---
# Add messages to an Azure Storage queue using Functions

In Azure Functions, input and output bindings provide a declarative way to connect to external service data from your function. In this topic, learn how to update an existing function by adding an output binding that sends messages to Azure Queue storage.  

![View message in the logs.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## Prerequisites 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Install the [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="add-binding"></a>Add an output binding
 
1. Expand both your function app and your function.

2. Select **Integrate** and **+ New output**, then choose **Azure Queue storage** and choose **Select**.
    
    ![Add a Queue storage output binding to a function in the Azure portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Use the settings as specified in the table: 

    ![Add a Queue storage output binding to a function in the Azure portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Setting      |  Suggested value   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Queue name**   | myqueue-items    | The name of the queue to connect to in your Storage account. |
    | **Storage account connection** | AzureWebJobStorage | You can use the storage account connection already being used by your function app, or create a new one.  |
    | **Message parameter name** | outputQueueItem | The name of the output binding parameter. | 

4. Click **Save** to add the binding.
 
Now that you have an output binding defined, you need to update the code to use the binding to add messages to a queue.  

## Update the function code

1. Select your function to display the function code in the editor. 

2. For a C# function, update your function definition as follows to add the **outputQueueItem** storage binding parameter. Skip this step for a JavaScript function.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outputQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Add the following code to the function just before the method returns. Use the appropriate snippet for the language of your function.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Select **Save** to save changes.

The value passed to the HTTP trigger is included in a message added to the queue.
 
## Test the function 

1. After the code changes are saved, select **Run**. 

    ![Add a Queue storage output binding to a function in the Azure portal.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Check the logs to make sure that the function succeeded. A new queue named **outqueue** is created in your Storage account by the Functions runtime when the output binding is first used.

Next, you can connect to your storage account to verify the new queue and the message you added to it. 

## Connect to the queue

Skip the first three steps if you have already installed Storage Explorer and connected it to your storage account.    

1. In your function, choose **Integrate** and the new **Azure Queue storage** output binding, then expand **Documentation**. Copy both **Account name** and **Account key**. You use these credentials to connect to the storage account.
 
    ![Get the Storage account connection credentials.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Run the [Microsoft Azure Storage Explorer](http://storageexplorer.com/) tool, select the connect icon on the left, choose **Use a storage account name and key**, and select **Next**.

    ![Run the Storage Account Explorer tool.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Paste the **Account name** and **Account key** from step 1 into their corresponding fields, then select **Next**, and **Connect**. 
  
    ![Paste the storage credentials and connect.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expand the attached storage account, expand **Queues** and verify that a queue named **myqueue-items** exists. You should also see a message already in the queue.  
 
    ![Create a storage queue.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## Clean up resources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## Next steps

You have added an output binding to an existing function. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

For more information about binding to Queue storage, see [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md). 



