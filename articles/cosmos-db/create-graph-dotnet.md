---
title: Build an Azure Cosmos DB .NET Framework or Core application using the Graph API | Microsoft Docs
description: Presents a .NET Framework/Core code sample you can use to connect to and query Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: dennyglee
manager: jhubbard
editor: ''

ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: denlee

---
# Azure Cosmos DB: Build a .NET Framework or Core application using the Graph API

Azure Cosmos DB is Microsoft’s globally distributed multi-model database service. You can quickly create and query document, key/value, and graph databases, all of which benefit from the global distribution and horizontal scale capabilities at the core of Azure Cosmos DB. 

This quick start demonstrates how to create an Azure Cosmos DB account, database, and graph (container) using the Azure portal. You then build and run a console app built on the [Graph API](graph-sdk-dotnet.md) (preview).  

## Prerequisites

If you don’t already have Visual Studio 2017 installed, you can download and use the **free** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Make sure that you enable **Azure development** during the Visual Studio setup.

If you already have Visual Studio 2017 installed, make sure to be installed up to [Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create a database account

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## Add a graph

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## Clone the sample application

Now let's clone a Graph API app from github, set the connection string, and run it. You'll see how easy it is to work with data programmatically. 

This sample project is using .NET Core project format and has been configured to target the following frameworks:
 - netcoreapp2.0
 - net461

1. Open a git terminal window, such as git bash, and `cd` to a working directory.  

2. Run the following command to clone the sample repository. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Then open Visual Studio and open the solution file. 

## Review the code

Let's make a quick review of what's happening in the app. Open the Program.cs file and you'll find that these lines of code create the Azure Cosmos DB resources. 

* The DocumentClient is initialized. In the preview, we added a graph extension API on the Azure Cosmos DB client. We are working on a standalone graph client decoupled from the Azure Cosmos DB client and resources.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* A new database is created.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* A new graph is created.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* A series of Gremlin steps are executed using the `CreateGremlinQuery` method.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## Update your connection string

Now go back to the Azure portal to get your connection string information and copy it into the app.

1. In Visual Studio 2017, open the appsettings.json file. 

2. In the Azure portal, in your Azure Cosmos DB account, click **Keys** in the left navigation. 

    ![View and copy an primary key in the Azure portal, on the Keys page](./media/create-graph-dotnet/keys.png)

3. Copy your **URI** value from the portal and make it the value of the Endpoint key in appsettings.json. You can use the copy button as shown in the preceding screenshot to copy the value.

    `"endpoint": "https://FILLME.documents.azure.com:443/",`

4. Copy your **PRIMARY KEY** value from the portal, and make it the value of the AuthKey key in App.config, then save your changes. 

    `"authkey": "FILLME"`

You've now updated your app with all the info it needs to communicate with Azure Cosmos DB. 

## Run the console app

Before running the application, it is recommended that you update the *Microsoft.Azure.Graphs* package to the latest version.

1. In Visual Studio, right-click on the **GraphGetStarted** project in **Solution Explorer** and then click **Manage NuGet Packages**. 

2. In the NuGet Package Manager **Updates** tab, type *Microsoft.Azure.Graphs* and check the **Includes prerelease** box. 

3. From the results, update the **Microsoft.Azure.Graphs** library to the latest version of the package. This installs the Azure Cosmos DB graph extension library package and all dependencies.

    If you get a message about reviewing changes to the solution, click **OK**. If you get a message about license acceptance, click **I accept**.

4. Click CTRL + F5 to run the application.

   The console window displays the vertexes and edges being added to the graph. When the script completes, press ENTER twice to close the console window.

## Browse using the Data Explorer

You can now go back to Data Explorer in the Azure portal and browse and query your new graph data.

1. In Data Explorer, the new database appears in the Graphs pane. Expand **graphdb**, **graphcollz**, and then click **Graph**.

2. Click the **Apply Filter** button to use the default query to view all the verticies in the graph. The data generated by the sample app is displayed in the Graphs pane.

    You can zoom in and out of the graph, you can expand the graph display space, add additional verticies, and move verticies on the display surface.

    ![View the graph in Data Explorer in the Azure portal](./media/create-graph-dotnet/graph-explorer.png)

## Review SLAs in the Azure portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## Clean up resources

If you're not going to continue to use this app, delete all resources created by this quickstart in the Azure portal with the following steps: 

1. From the left-hand menu in the Azure portal, click **Resource groups** and then click the name of the resource you created. 
2. On your resource group page, click **Delete**, type the name of the resource to delete in the text box, and then click **Delete**.

## Next steps

In this quickstart, you've learned how to create an Azure Cosmos DB account, create a graph using the Data Explorer, and run an app. You can now build more complex queries and implement powerful graph traversal logic using Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md)

