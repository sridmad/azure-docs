---
title: Publish your LUIS app | Microsoft Docs
description: After you build and test your app by using Language Understanding Intelligent Services (LUIS), publish it as a web service on Azure. 
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 09/26/2017
ms.author: cahann
---


# Publish your app
When you finish building and testing your LUIS app, you can publish it as a web service on Azure and get an HTTP endpoint that can be integrated into any client or backend code. 

> [!TIP]
> You can optionally test your app before publishing it. For instructions, see [Train and test your app](Train-Test.md).

You can either publish your app directly to the **Production Slot** where end users can access and use your model, or you can publish to a **Staging Slot** for working through trials and tests to validate changes before publishing to the production slot. 

## Publish your app to an HTTP endpoint

1. Open your app by clicking its name on the **My Apps** page, and then click **Publish App** in the left panel. The following screenshot shows the **Publish App** page if you haven't published your app yet.

    ![Publish page-](./media/luis-how-to-publish-app/luis-first-publish.png)
 
    If you have previously published this app, this page looks like the following screenshot: 
 
    ![Publish page](./media/luis-how-to-publish-app/luis-republish.png)
2. The first time you publish, the **Publish App** page shows your starter key. If you want to use a key other than the keys shown, click the **Add Key** button. This action opens a dialog that allows you to select an existing endpoint key to assign to the app. For more information on how to create and add endpoint keys to your LUIS app, see [Manage your keys](Manage-Keys.md).
3. Choose whether to publish to **Production** or to **Staging** by selecting from the drop-down menu under **Publish to**. 
4. If you want to enable Bing Spell Check, click the **Enable Bing Spell Checker** check box. 

    >[!NOTE]
    >For a limited time, the option to enable Bing Spell Check is included with your LUIS subscription. However, you need to add `spellCheck=true` to the URL when you call the LUIS app endpoint to turn on spell checking. Checking the **Enable Bing Spell Checker** check box appends `spellCheck=true` to the URL that displays in the **Publish app** page when publish is complete. 


5. If you want the JSON response of your published app to include all intents defined in your app and their prediction scores, click **Add Verbose Flag** checkbox to append a `verbose=true` parameter to the end point URL. Otherwise, it includes only the top scoring intent.
6. If the app has not been trained, click **Train**.  <!-- todo: check -->

7. Click **Publish to production slot** if you have selected the **Production** option under **Publish to**, or **Publish to staging slot** if you have selected **Staging**. If publish succeeds, you can use the displayed endpoint URL to access your LUIS app. 

    >[!NOTE]
    >If the **Publish** button is disabled, then either your app does not have an assigned an endpoint key, or you have not trained your app yet.

    ![Endpoint URL displayed in Publish page](./media/luis-how-to-publish-app/luis-publish-url.png)

    > [!TIP]
    > The endpoint URL corresponds to the Azure region associated with the endpoint key. To see endpoints and keys associated with other regions, use the radio buttons to switch regions. Each row in the **Resources and Keys** table lists Azure resource associated with your account and the endpoint keys associated with that resource, For more information, see [Regions and keys](./Manage-Keys.md#regions-and-keys).


## Test your published endpoint in a browser
You can test your published endpoint in a browser using the generated URL. Copy the URL, then replace the `{YOUR-KEY-HERE}` with one of the keys listed in the **Key String** column for the resource you want to use. To open this URL in your browser, set the URL parameter "&q" to your test query. For example, append `&q=Book me a flight to Boston on May 4` to your URL, and then press Enter. The browser displays the JSON response of your HTTP endpoint. 

![JSON response from a published HTTP endpoint](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)


## Next steps

* See [Manage keys](./Manage-Keys.md) to add keys to your LUIS app, and learn about how keys map to regions.
* See [Train and test your app](Train-Test.md) for instructions on how to test your published app in the test console.
