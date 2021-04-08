---
title: Azure Functions Microsoft Graph bindings | Microsoft Docs
description: Understand how to use Microsoft Graph triggers and bindings in Azure Functions.
services: functions
author: mattchenderson
manager: cfowler
editor: ''

ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender

---

# Azure Functions Microsoft Graph bindings
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

This article explains how to configure and work with Microsoft Graph triggers and bindings in Azure Functions.
With these, you can use Azure Functions to work with data, insights, and events from the [Microsoft Graph](https://graph.microsoft.io).

The Microsoft Graph extension provides the following bindings:
- An [auth token input binding](#token-input) allows you to interact with any Microsoft Graph API.
- An [Excel table input binding](#excel-input) allows you to read data from Excel.
- An [Excel table output binding](#excel-output) allows you to modify Excel data.
- An [OneDrive file input binding](#onedrive-input) allows you to read files from OneDrive.
- An [OneDrive file output binding](#onedrive-output) allows you to write to files in OneDrive.
- An [Outlook message output binding](#outlook-output) allows you to send email through Outlook.
- A collection of [Microsoft Graph webhook triggers and bindings](#webhooks) allows you to react to events from the Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Microsoft Graph bindings are currently in preview.

## Setting up the extensions

Microsoft Graph bindings are available through _binding extensions_. Binding extensions are optional components to the Azure Functions runtime. This section will show you how to set up the Microsoft Graph and auth token extensions.

### Enabling Functions 2.0 preview

Binding extensions are only available only for Azure Functions 2.0 preview. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

To learn more, see [How to target Azure Functions runtime versions](functions-versions.md).

### Installing the extension

To install an extension from the Azure portal, you need to navigate to either a template or binding which references it. Create a new function, and while in the template selection screen, choose the "Microsoft Graph" scenario. Select one of the templates from this scenario. Alternatively, you can navigate to the "Integrate" tab of an existing function and select one of the bindings covered in this topic.

In both cases, a warning will appear which specifies the extension to be installed. Click **Install** to obtain the extension.

> [!Note] 
> Each extension only needs to be installed once per function app. The in-portal installation process can take up to 10 minutes on a consumption plan.

If you are using Visual Studio, you can get the extensions by installing these NuGet packages:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### Configuring App Service Authentication / Authorization

The bindings outlined in this topic require an identity to be used. This allows the Microsoft Graph to enforce permissions and audit interactions. The identity can be a user accessing your application or the application itself. To configure this identity, you will need to set up [App Service Authentication / Authorization](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) with Azure Active Directory. You will also need to request any resource permissions your functions require.

> [!Note] 
> The Microsoft Graph extension only supports AAD authentication. Users need to log in with a work or school account.

If using the Azure portal, below the prompt to install the extension, you will see a warning which prompts you to configure App Service Authentication / Authorization and request any permissions the template or binding requires. Click **Configure AAD now** or **Add permissions now** as appropriate.






<a name="token-input"></a>
## Auth token input binding

This binding gets an AAD token for a given resource and provides it to your code as a string. The resource can be any for which the application has permissions. 

### Configuring an auth token input binding

The binding itself does not require any AAD permissions, but depending on how the token is used, you may need to request additional permissions. Check the requirements of the resource you intend to access with the token.

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the auth token. See [Using an auth token input binding from code](#token-input-code).|
|**type**|Required - must be set to `token`.|
|**direction**|Required - must be set to `in`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**resource**|Required - An AAD resource URL for which the token is being requested.|

<a name="token-input-code"></a>
### Using an auth token input binding from code

The token is always presented to code as a string.

#### Sample: Getting user profile information

Suppose you have the following function.json that defines an HTTP trigger with a token input binding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample uses the token to make an HTTP call to the Microsoft Graph and returns the result:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

The following JS sample uses the token to make an HTTP call to the Microsoft Graph and returns the result. In the `function.json` above, change `$return` to `res` first.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## Excel table input binding

This binding reads the contents of an Excel table stored in OneDrive.

### Configuring an Excel table input binding

This binding requires the following AAD permissions:
|Resource|Permission|
|--------|--------|
|Microsoft Graph|Read user files|

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the Excel table. See [Using an Excel table input binding from code](#excel-input-code).|
|**type**|Required - must be set to `excel`.|
|**direction**|Required - must be set to `in`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**path**|Required - the path in OneDrive to the Excel workbook.|
|**worksheetName**|The worksheet in which the table is found.|
|**tableName**|The name of the table. If not specified, the contents of the worksheet will be used.|

<a name="excel-input-code"></a>
### Using an Excel table input binding from code

The binding exposes the following types to .NET functions:
- string[][]
- Microsoft.Graph.WorkbookTable
- Custom object types (using structural model binding)

#### Sample: Reading an Excel table

Suppose you have the following function.json that defines an HTTP trigger with an Excel input binding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample adds reads the contents of the specified table and returns them to the user:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

The following JS sample adds reads the contents of the specified table and returns them to the user. In the `function.json` above, change `$return` to `res` first.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## Excel table output binding

This binding modifies the contents of an Excel table stored in OneDrive.

### Configuring an Excel table output binding

This binding requires the following AAD permissions:
|Resource|Permission|
|--------|--------|
|Microsoft Graph|Have full access to user files|

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the auth token. See [Using an Excel table output binding from code](#excel-output-code).|
|**type**|Required - must be set to `excel`.|
|**direction**|Required - must be set to `out`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**path**|Required - the path in OneDrive to the Excel workbook.|
|**worksheetName**|The worksheet in which the table is found.|
|**tableName**|The name of the table. If not specified, the contents of the worksheet will be used.|
|**updateType**|Required - The type of change to make to the table. Can be one of the following values:<ul><li><code>update</code> - Replaces the contents of the table in OneDrive.</li><li><code>append</code> - Adds the payload to the end of the table in OneDrive by creating new rows.</li></ul>|

<a name="excel-output-code"></a>
### Using an Excel table output binding from code

The binding exposes the following types to .NET functions:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Custom object types (using structural model binding)

#### Sample: Adding rows to an Excel table

Suppose you have the following function.json that defines an HTTP trigger with an Excel output binding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


The following C# sample adds a new row to the table (assumed to be single-column) based on input from the query string:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

The following JS sample adds a new row to the table (assumed to be single-column) based on input from the query string. In the `function.json` above, change `$return` to `res` first.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## OneDrive file input binding

This binding reads the contents of a file stored in OneDrive.

### Configuring a OneDrive file input binding

This binding requires the following AAD permissions:
|Resource|Permission|
|--------|--------|
|Microsoft Graph|Read user files|

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the file. See [Using a OneDrive file input binding from code](#onedrive-input-code).|
|**type**|Required - must be set to `onedrive`.|
|**direction**|Required - must be set to `in`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**path**|Required - the path in OneDrive to the file.|

<a name="onedrive-input-code"></a>
### Using a OneDrive file input binding from code

The binding exposes the following types to .NET functions:
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### Sample: Reading a file from OneDrive

Suppose you have the following function.json that defines an HTTP trigger with a OneDrive input binding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample reads the file specified in the query string and logs its length:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

The following JS sample reads the file specified in the query string and returns its length. In the `function.json` above, change `$return` to `res` first.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## OneDrive file output binding

This binding modifies the contents of a file stored in OneDrive.

### Configuring a OneDrive file output binding

This binding requires the following AAD permissions:
|Resource|Permission|
|--------|--------|
|Microsoft Graph|Have full access to user files|

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for file. See [Using a OneDrive file output binding from code](#onedrive-output-code).|
|**type**|Required - must be set to `onedrive`.|
|**direction**|Required - must be set to `out`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**path**|Required - the path in OneDrive to the file.|

<a name="onedrive-output-code"></a>
### Using a OneDrive file output binding from code

The binding exposes the following types to .NET functions:
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### Sample: Writing to a file in OneDrive

Suppose you have the following function.json that defines an HTTP trigger with a OneDrive output binding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample gets text from the query string and writes it to a text file (FunctionsTest.txt as defined in the config above) at the root of the caller's OneDrive:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
The following JS sample gets text from the query string and writes it to a text file (FunctionsTest.txt as defined in the config above) at the root of the caller's OneDrive. In the `function.json` above, change `$return` to `res` first.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## Outlook message output binding

Sends a mail message through Outlook.

### Configuring an Outlook message output binding

This binding requires the following AAD permissions:
|Resource|Permission|
|--------|--------|
|Microsoft Graph|Send mail as user|

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the mail message. See [Using an Outlook message output binding from code](#outlook-output-code).|
|**type**|Required - must be set to `outlook`.|
|**direction**|Required - must be set to `out`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |

<a name="outlook-output-code"></a>
### Using an Outlook message output binding from code

The binding exposes the following types to .NET functions:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Custom object types (using structural model binding)

#### Sample: Sending an email through Outlook

Suppose you have the following function.json that defines an HTTP trigger with an Outlook message output binding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

The following C# sample sends a mail from the caller to a recipient specified in the query string:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

The following JS sample sends a mail from the caller to a recipient specified in the query string:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## Microsoft Graph webhook bindings

Webhooks allow you to react to events in the Microsoft Graph. To support webhooks, functions are needed to create, refresh, and react to _webhook subscriptions_. A complete webhook solution will require a combination of the following bindings:
- A [Microsoft Graph webhook trigger](#webhook-trigger) allows you to react to an incoming webhook.
- A [Microsoft Graph webhook subscription input binding](#webhook-input) allows you to list existing subscriptions and optionally refresh them.
- A [Microsoft Graph webhook subscription output binding](#webhook-output) allows you to create or delete webhook subscriptions.

The bindings themselves do not require any AAD permissions, but you will need to request permissions relevant to the resource type you wish to react to. For a list of which permissions are needed for each resource type, see [subscription permissions](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

For more about webhooks, see [Working with webhooks in Microsoft Graph].





<a name="webhook-trigger"></a>
### Microsoft Graph webhook trigger

This trigger allows a function to react to an incoming webhook from the Microsoft Graph. Each instance of this trigger can react to one Microsoft Graph resource type.

#### Configuring a Microsoft Graph webhook trigger

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the mail message. See [Using an Outlook message output binding from code](#outlook-output-code).|
|**type**|Required - must be set to `graphWebhook`.|
|**direction**|Required - must be set to `trigger`.|
|**resourceType**|Required - the graph resource for which this function should respond to webhooks. Can be one of the following values:<ul><li><code>#Microsoft.Graph.Message</code> - changes made to Outlook messages.</li><li><code>#Microsoft.Graph.DriveItem</code> - changes made to OneDrive root items.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> A function app can only have one function which is registered against a given `resourceType` value.

#### Using a Microsoft Graph webhook trigger from code

The binding exposes the following types to .NET functions:
- Microsoft Graph SDK types relevant to the resource type, e.g., Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Custom object types (using structural model binding)

For examples of using this binding in code, please see [Microsoft Graph webhook samples](#webhook-samples).



<a name="webhook-input"></a>
### Microsoft Graph webhook subscription input binding

This binding allows you to retrieve the list of subscriptions managed by this function app. The binding reads from function app storage, and does not reflect other subscriptions created from outside the app.

#### Configuring a Microsoft Graph webhook subscription input binding

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the mail message. See [Using an Outlook message output binding from code](#outlook-output-code).|
|**type**|Required - must be set to `graphWebhookSubscription`.|
|**direction**|Required - must be set to `in`.|
|**filter**| If set to `userFromRequest`, then the binding will only retrieve subscriptions owned by the calling user ( valid only with [HTTP trigger]).| 

#### Using a Microsoft Graph webhook subscription input binding from code

The binding exposes the following types to .NET functions:
- string[]
- Custom object type arrays
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

For examples of using this binding in code, please see [Microsoft Graph webhook samples](#webhook-samples).


<a name="webhook-output"></a>
### Microsoft Graph webhook subscription output binding

This binding allows you to create, delete, and refresh webhook subscriptions in the Microsoft Graph.

#### Configuring a Microsoft Graph webhook subscription output binding

The binding supports the following properties:

|Property|Description|
|--------|--------|
|**name**|Required - the variable name used in function code for the mail message. See [Using an Outlook message output binding from code](#outlook-output-code).|
|**type**|Required - must be set to `graphWebhookSubscription`.|
|**direction**|Required - must be set to `out`.|
|**identity**|Required - The identity that will be used to perform the action. Can be one of the following values:<ul><li><code>userFromRequest</code> - Only valid with [HTTP trigger]. Uses the identity of the calling user.</li><li><code>userFromId</code> - Uses the identity of a previously logged-in user with the specified ID. See the <code>userId</code> property.</li><li><code>userFromToken</code> - Uses the identity represented by the specified token. See the <code>userToken</code> property.</li><li><code>clientCredentials</code> - Uses the identity of the function app.</li></ul>|
|**userId**	|Needed if and only if _identity_ is set to `userFromId`. A user principal ID associated with a previously logged-in user.|
|**userToken**|Needed if and only if _identity_ is set to `userFromToken`. A token valid for the function app. |
|**action**|Required - specifies the action the binding should perform. Can be one of the following values:<ul><li><code>create</code> - Registers a new subscription.</li><li><code>delete</code> - Deletes a specified subscription.</li><li><code>refresh</code> - Refreshes a specified subscription to keep it from expiring.</li></ul>|
|**subscriptionResource**|Needed if and only if the _action_ is set to `create`. Specifies the Microsoft Graph resource that will be monitored for changes. See [Working with webhooks in Microsoft Graph]. |
|**changeType**|Needed if and only if the _action_ is set to `create`. Indicates the type of change in the subscribed resource that will raise a notification. The supported values are: `created`, `updated`, `deleted`. Multiple values can be combined using a comma-separated list.|

#### Using a Microsoft Graph webhook subscription output binding from code

The binding exposes the following types to .NET functions:
- string
- Microsoft.Graph.Subscription

For examples of using this binding in code, please see [Microsoft Graph webhook samples](#webhook-samples).
 
<a name="webhook-samples"></a>
### Microsoft Graph webhook samples

#### Sample: Creating a subscription

Suppose you have the following function.json that defines an HTTP trigger with a subscription output binding using the create action:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample registers a webhook that will notify this function app when the calling user receives an Outlook message:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
	clientState = Guid.NewGuid().ToString();
	return new HttpResponseMessage(HttpStatusCode.OK);
}
```

The following JS sample registers a webhook that will notify this function app when the calling user receives an Outlook message:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### Sample: Handling notifications

Suppose you have the following function.json that defines Graph webhook trigger to handle Outlook messages:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

The following C# sample reacts to incoming mail messages and logs the body of those sent by the recipient and containing "Azure Functions" in the subject:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

The following JS sample reacts to incoming mail messages and logs the body of those sent by the recipient and containing "Azure Functions" in the subject:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### Sample: Deleting subscriptions

Suppose you have the following function.json that defines an HTTP trigger with a subscription input binding and a subscription output binding using the delete action:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

The following C# sample gets all subscriptions for the calling user and then deletes them:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

The following JS sample gets all subscriptions for the calling user and then deletes them:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### Sample: Refreshing subscriptions

There are two approaches to refreshing subscriptions:
- Use the application identity to deal with all subscriptions. This will require consent from an Azure Active Directory admin. This can be used by all languages supported by Azure Functions.
- Use the identity associated with each subscription by manually binding each user ID. This will require some custom code to perform the binding. This can only be used by .NET functions.

Both options are shown below.

**Using the application identity**

Suppose you have the following function.json that defines a timer trigger with a subscription input binding  subscription output binding, using the application identity:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

The following C# sample refreshes the subscriptions on a timer, using the application identity:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

The following JS sample refreshes the subscriptions on a timer, using the application identity:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Using dynamic user identities**

For the alternative option, suppose you have the following function.json that defines a timer trigger, deferring binding to the subscription input binding to the function code:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

The following C# sample refreshes the subscriptions on a timer, using each user's identity by creating the output binding in code:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
	foreach (var subscription in existingSubscriptions)
	{
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
    		log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[HTTP trigger]: functions-bindings-http-webhook.md
[Working with webhooks in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
