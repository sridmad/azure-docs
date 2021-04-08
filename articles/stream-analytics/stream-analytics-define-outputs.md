---
title: 'Stream Analytics outputs: Options for storage, analysis | Microsoft Docs'
description: Learn about targeting Stream Analytics data outputs options including Power BI for analysis results.
keywords: data transformation, analysis results, data storage options
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: ''
author: samacha
manager: jhubbard
editor: cgronlun

ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha

---
# Stream Analytics outputs: Options for storage, analysis
When authoring a Stream Analytics job, consider how the resulting data will be consumed. How will you view the results of the Stream Analytics job and where will you store it?

In order to enable a variety of application patterns, Azure Stream Analytics has different options for storing output and viewing analysis results. This makes it easy to view job output and gives you flexibility in the consumption and storage of the job output for data warehousing and other purposes. Any output configured in the job must exist before the job is started and events start flowing. For example, if you use Blob storage as an output, the job will not create a storage account automatically. It needs to be created by the user before the ASA job is started.

## Azure Data Lake Store
Stream Analytics supports [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). This storage enables you to store data of any size, type and ingestion speed for operational and exploratory analytics. Further, Stream Analytics needs to be authorized to access the Data Lake Store. Details on authorization and how to sign up for the Data Lake Store (if needed) are discussed in the [Data Lake output article](stream-analytics-data-lake-output.md).

### Authorize an Azure Data Lake Store
When Data Lake Storage is selected as an output in the Azure portal, you will be prompted to authorize a connection to an existing Data Lake Store.  

![Authorize Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Then fill out the properties for the Data Lake Store output as seen below:

![Authorize Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

The table below lists the property names and their description needed for creating a Data Lake Store output.

<table>
<tbody>
<tr>
<td><B>PROPERTY NAME</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Output Alias</td>
<td>This is a friendly name used in queries to direct the query output to this Data Lake Store.</td>
</tr>
<tr>
<td>Account Name</td>
<td>The name of the Data Lake Storage account where you are sending your output. You will be presented with a drop down list of Data Lake Store accounts to which the user logged in to the portal has access to.</td>
</tr>
<tr>
<td>Path Prefix Pattern</td>
<td>File naming will follow the following convention: <BR>{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension <BR> <BR>Example output files:<BR>Myoutput/20170901/00/45434_gguid_1.csv <BR>Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR>Also, here are the situations, where a new file is created:<BR>1. Change in output schema <BR>2. External or Internal restart of a job<BR><BR>Additionally, if the file path pattern does not contain a trailing “/”, the last pattern in the file path will be treated as a filename prefix.<BR><BR>Example:<BR>For the path pattern: folder1/logs/HH, the generated file may look like: folder1/logs/02_134343_gguid_1.csv</td>
</tr>
<tr>
<td>Date Format [<I>optional</I>]</td>
<td>If the date token is used in the prefix path, you can select the date format in which your files are organized. Example: YYYY/MM/DD</td>
</tr>
<tr>
<td>Time Format [<I>optional</I>]</td>
<td>If the time token is used in the prefix path, specify the time format in which your files are organized. Currently the only supported value is HH.</td>
</tr>
<tr>
<td>Event Serialization Format</td>
<td>Serialization format for output data. JSON, CSV, and Avro are supported.</td>
</tr>
<tr>
<td>Encoding</td>
<td>If CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time.</td>
</tr>
<tr>
<td>Delimiter</td>
<td>Only applicable for CSV serialization. Stream Analytics supports a number of common delimiters for serializing CSV data. Supported values are comma, semicolon, space, tab and vertical bar.</td>
</tr>
<tr>
<td>Format</td>
<td>Only applicable for JSON serialization. Line separated specifies that the output will be formatted by having each JSON object separated by a new line. Array specifies that the output will be formatted as an array of JSON objects. This array will be closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it is preferable to use line separated JSON, since it doesn't require any special handling while the output file is still being written to.</td>
</tr>
</tbody>
</table>

### Renew Data Lake Store Authorization
You will need to re-authenticate your Data Lake Store account if its password has changed since your job was created or last authenticated.

![Authorize Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) can be used as an output for data that is relational in nature or for applications that depend on content being hosted in a relational database. Stream Analytics jobs will write to an existing table in an Azure SQL Database.  Note that the table schema must exactly match the fields and their types being output from your job. An [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) can also be specified as an output via the SQL Database output option as well (this is a preview feature). The table below lists the property names and their description for creating a SQL Database output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this database. |
| Database |The name of the database where you are sending your output |
| Server Name |The SQL Database server name |
| Username |The Username which has access to write to the database |
| Password |The password to connect to the database |
| Table |The table name where the output will be written. The table name is case sensitive and the schema of this table should match exactly to the number of fields and their types being generated by your job output. |

> [!NOTE]
> Currently the Azure SQL Database offering is supported for a job output in Stream Analytics. However, an Azure Virtual Machine running SQL Server with a database attached is not supported. This is subject to change in future releases.
> 
> 

## Blob storage
Blob storage offers a cost-effective and scalable solution for storing large amounts of unstructured data in the cloud.  For an introduction on Azure Blob storage and its usage, see the documentation at [How to use Blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

The table below lists the property names and their description for creating a blob output.

<table>
<tbody>
<tr>
<td>PROPERTY NAME</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Output Alias</td>
<td>This is a friendly name used in queries to direct the query output to this blob storage.</td>
</tr>
<tr>
<td>Storage Account</td>
<td>The name of the storage account where you are sending your output.</td>
</tr>
<tr>
<td>Storage Account Key</td>
<td>The secret key associated with the storage account.</td>
</tr>
<tr>
<td>Storage Container</td>
<td>Containers provide a logical grouping for blobs stored in the Microsoft Azure Blob service. When you upload a blob to the Blob service, you must specify a container for that blob.</td>
</tr>
<tr>
<td>Path Prefix Pattern [optional]</td>
<td>The file path pattern used to write your blobs within the specified container. <BR> In the path pattern, you may choose to use one or more instances of the following 2 variables to specify the frequency that blobs are written: <BR> {date}, {time} <BR> Example 1: cluster1/logs/{date}/{time} <BR> Example 2: cluster1/logs/{date} <BR> <BR> File naming will follow the following convention: <BR> {Path Prefix Pattern}/schemaHashcode_Guid_Number.extension <BR> <BR> Example output files: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Also, here are the situations, where a new file is created: <BR> 1. Current file exceeds the maximum permissible number of blocks (currently 50,000) <BR> 2. Change in output schema <BR> 3. External or internal restart of a job  </td>
</tr>
<tr>
<td>Date Format [optional]</td>
<td>If the date token is used in the prefix path, you can select the date format in which your files are organized. Example: YYYY/MM/DD</td>
</tr>
<tr>
<td>Time Format [optional]</td>
<td>If the time token is used in the prefix path, specify the time format in which your files are organized. Currently the only supported value is HH.</td>
</tr>
<tr>
<td>Event Serialization Format</td>
<td>Serialization format for output data.  JSON, CSV, and Avro are supported.</td>
</tr>
<tr>
<td>Encoding</td>
<td>If CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time.</td>
</tr>
<tr>
<td>Delimiter</td>
<td>Only applicable for CSV serialization. Stream Analytics supports a number of common delimiters for serializing CSV data. Supported values are comma, semicolon, space, tab and vertical bar.</td>
</tr>
<tr>
<td>Format</td>
<td>Only applicable for JSON serialization. Line separated specifies that the output will be formatted by having each JSON object separated by a new line. Array specifies that the output will be formatted as an array of JSON objects. This array will be closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it is preferable to use line separated JSON, since it doesn't require any special handling while the output file is still being written to.</td>
</tr>
</tbody>
</table>

## Event Hub
[Event Hubs](https://azure.microsoft.com/services/event-hubs/) is a highly scalable publish-subscribe event ingestor. It can collect millions of events per second.  One use of an Event Hub as output is when the output of a Stream Analytics job will be the input of another streaming job.

There are a few parameters that are needed to configure Event Hub data streams as an output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this Event Hub. |
| Service Bus Namespace |A Service Bus namespace is a container for a set of messaging entities. When you created a new Event Hub, you also created a Service Bus namespace |
| Event Hub |The name of your Event Hub output |
| Event Hub Policy Name |The shared access policy, which can be created on the Event Hub Configure tab. Each shared access policy will have a name, permissions that you set, and access keys |
| Event Hub Policy Key |The Shared Access key used to authenticate access to the Service Bus namespace |
| Partition Key Column [optional] |This column contains the partition key for Event Hub output. |
| Event Serialization Format |Serialization format for output data.  JSON, CSV, and Avro are supported. |
| Encoding |For CSV and JSON, UTF-8 is the only supported encoding format at this time |
| Delimiter |Only applicable for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab and vertical bar. |
| Format |Only applicable for JSON serialization. Line separated specifies that the output will be formatted by having each JSON object separated by a new line. Array specifies that the output will be formatted as an array of JSON objects. This array will be closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it is preferable to use line separated JSON, since it doesn't require any special handling while the output file is still being written to. |

## Power BI
[Power BI](https://powerbi.microsoft.com/) can be used as an output for a Stream Analytics job to provide for a rich visualization experience of analysis results. This capability can be used for operational dashboards, report generation and metric driven reporting.

### Authorize a Power BI account
1. When Power BI is selected as an output in the Azure portal, you will be prompted to authorize an existing Power BI User or to create a new Power BI account.  
   
   ![Authorize Power BI User](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Create a new account if you don’t yet have one, then click Authorize Now.  A screen like the following is presented.  
   
   ![Azure Account Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. In this step, provide the work or school account for authorizing the Power BI output. If you are not already signed up for Power BI, choose Sign up now. The work or school account you use for Power BI could be different from the Azure subscription account which you are currently logged in with.

### Configure the Power BI output properties
Once you have the Power BI account authenticated, you can configure the properties for your Power BI output. The table below is the list of property names and their description to configure your Power BI output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this PowerBI output. |
| Group Workspace |To enable sharing data with other Power BI users you can select groups inside your Power BI account or choose “My Workspace” if you do not want to write to a group.  Updating an existing group requires renewing the Power BI authentication. |
| Dataset Name |Provide a dataset name that it is desired for the Power BI output to use |
| Table Name |Provide a table name under the dataset of the Power BI output. Currently, Power BI output from Stream Analytics jobs can only have one table in a dataset |

For a walk-through of configuring a Power BI output and dashboard, please see the [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) article.

> [!NOTE]
> Do not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t generate any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.
> 
> 

### Schema Creation
Azure Stream Analytics creates a Power BI dataset and table on behalf of the user if one does not already exist. In all other cases, the table is updated with new values.Currently, there is a the limitation that only one table can exist within a dataset.

### Data type conversion from ASA to Power BI
Azure Stream Analytics updates the data model dynamically at runtime if the output schema changes. Column name changes, column type changes, and the addition or removal of columns are all tracked.

This table covers the data type conversions from [Stream Analytics data types](https://msdn.microsoft.com/library/azure/dn835065.aspx) to Power BIs [Entity Data Model (EDM) types](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) if a POWER BI dataset and table do not exist.


From Stream Analytics | To Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | String
datetime | Datetime
float | Double
Record array | String type, Constant value “IRecord” or “IArray”

### Schema Update
Stream Analytics infers the data model schema based on the first set of events in the output. Later, if necessary, the data model schema is updated to accommodate incoming events that may not fit into the original schema.

The `SELECT *` query should be avoided to prevent dynamic schema update across rows. In addition to potential performance implications, it could also result in indeterminacy of the time taken for the results. The exact fields that need to be shown on Power BI dashboard should be selected. Additionally, the data values should be compliant with the chosen data type.


Previous/Current | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String |  | String | 
Datetime | String | String |  Datetime | String


### Renew Power BI Authorization
You will need to re-authenticate your Power BI account if its password has changed since your job was created or last authenticated. If Multi-Factor Authentication (MFA) is configured on your Azure Active Directory (AAD) tenant you will also need to renew Power BI authorization every 2 weeks. A symptom of this issue is no job output and an "Authenticate user error" in the Operation Logs:

  ![Power BI refresh token error](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

To resolve this issue, stop your running job and go to your Power BI output.  Click the “Renew authorization” link, and restart your job from the Last Stopped Time to avoid data loss.

  ![Power BI renew authorization](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## Table Storage
[Azure Table storage](../storage/common/storage-introduction.md)  offers highly available, massively scalable storage, so that an application can automatically scale to meet user demand. Table storage is Microsoft’s NoSQL key/attribute store which one can leverage for structured data with less constraints on the schema. Azure Table storage can be used to store data for persistence and efficient retrieval.

The table below lists the property names and their description for creating a table output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this table storage. |
| Storage Account |The name of the storage account where you are sending your output. |
| Storage Account Key |The access key associated with the storage account. |
| Table Name |The name of the table. The table will get created if it does not exist. |
| Partition Key |The name of the output column containing the partition key. The partition key is a unique identifier for the partition within a given table that forms the first part of an entity's primary key. It is a string value that may be up to 1 KB in size. |
| Row Key |The name of the output column containing the row key. The row key is a unique identifier for an entity within a given partition. It forms the second part of an entity’s primary key. The row key is a string value that may be up to 1 KB in size. |
| Batch Size |The number of records for a batch operation. Typically the default is sufficient for most jobs, refer to the [Table Batch Operation spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) for more details on modifying this setting. |
 
## Service Bus Queues
[Service Bus Queues](https://msdn.microsoft.com/library/azure/hh367516.aspx) offer a First In, First Out (FIFO) message delivery to one or more competing consumers. Typically, messages are expected to be received and processed by the receivers in the temporal order in which they were added to the queue, and each message is received and processed by only one message consumer.

The table below lists the property names and their description for creating a Queue output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this Service Bus Queue. |
| Service Bus Namespace |A Service Bus namespace is a container for a set of messaging entities. |
| Queue Name |The name of the Service Bus Queue. |
| Queue Policy Name |When you create a Queue, you can also create shared access policies on the Queue Configure tab. Each shared access policy will have a name, permissions that you set, and access keys. |
| Queue Policy Key |The Shared Access key used to authenticate access to the Service Bus namespace |
| Event Serialization Format |Serialization format for output data.  JSON, CSV, and Avro are supported. |
| Encoding |For CSV and JSON, UTF-8 is the only supported encoding format at this time |
| Delimiter |Only applicable for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab and vertical bar. |
| Format |Only applicable for JSON type. Line separated specifies that the output will be formatted by having each JSON object separated by a new line. Array specifies that the output will be formatted as an array of JSON objects. |

## Service Bus Topics
While Service Bus Queues provide a one to one communication method from sender to receiver, [Service Bus Topics](https://msdn.microsoft.com/library/azure/hh367516.aspx) provide a one-to-many form of communication.

The table below lists the property names and their description for creating a table output.

| Property Name | Description |
| --- | --- |
| Output Alias |This is a friendly name used in queries to direct the query output to this Service Bus Topic. |
| Service Bus Namespace |A Service Bus namespace is a container for a set of messaging entities. When you created a new Event Hub, you also created a Service Bus namespace |
| Topic Name |Topics are messaging entities, similar to event hubs and queues. They're designed to collect event streams from a number of different devices and services. When a topic is created, it is also given a specific name. The messages sent to a Topic will not be available unless a subscription is created, so ensure there are one or more subscriptions under the topic |
| Topic Policy Name |When you create a Topic, you can also create shared access policies on the Topic Configure tab. Each shared access policy will have a name, permissions that you set, and access keys |
| Topic Policy Key |The Shared Access key used to authenticate access to the Service Bus namespace |
| Event Serialization Format |Serialization format for output data.  JSON, CSV, and Avro are supported. |
 | Encoding |If CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time |
| Delimiter |Only applicable for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab and vertical bar. |

## Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is a globally distributed, multi-model database service that offers limitless elastic scale around the globe, rich query and automatic indexing over schema-agnostic data models, guaranteed low latency, and industry-leading comprehensive SLAs.

The below list details the property names and their description for creating an Azure Cosmos DB output.

* **Output Alias** – An alias to refer this output in your ASA query  
* **Account Name** – The name or endpoint URI of the Cosmos DB account.  
* **Account Key** – The shared access key for the Cosmos DB account.  
* **Database** – The Cosmos DB database name.  
* **Collection Name Pattern** – The collection name or their pattern for the collections to be used. The collection name format can be constructed using the optional {partition} token, where partitions start from 0. Following are sample valid inputs:  
  1\) MyCollection – One collection named “MyCollection” must exist.  
  2\) MyCollection{partition} – Such collections must exist– "MyCollection0”, “MyCollection1”, “MyCollection2” and so on.  
* **Partition Key** – Optional. This is only needed if you are using a {parition} token in your collection name pattern. The name of the field in output events used to specify the key for partitioning output across collections. For single collection output, any arbitrary output column can be used e.g. PartitionId.  
* **Document ID** – Optional. The name of the field in output events used to specify the primary key on which insert or update operations are based.  

## Azure Functions (In Preview)
Azure Functions is a serverless compute service that enables you to run code on-demand without having to explicitly provision or manage infrastructure. It lets you implement code that is triggered by events occurring in Azure or third-party services.  This ability of Azure Functions to respond to triggers makes it a natural output for a Azure Stream Analytics. This output adapter allows users to connect Stream Analytics to Azure Functions, and run a script or piece of code in response to a variety of events.

Azure Stream Analytics invokes Azure Functions via HTTP triggers. The new Azure Function Output adapter is available with the following configurable properties:

| Property Name | Description |
| --- | --- |
| Function App |Name of your Azure Functions App |
| Function |Name of the function in your Azure Functions App |
| Max Batch Size |This property can be used to set the maximum size for each output batch that will be sent to your Azure Function. By default, this value is 256 KB |
| Max Batch Count  |As the name indicates, this property lets you specify the maximum number of events in each batch that gets sent to Azure Functions. The default max batch count value is 100 |
| Key |If you want to use an Azure Function from another subscription, you can do so by providing the key to access your function |

Note that when Azure Stream Analytics receives 413 (http Request Entity Too Large) exception from Azure function, it reduces the size of the batches it sends to Azure Functions. In your Azure function code, use this exception to make sure that Azure Stream Analytics doesn’t send oversized batches. Also, please make sure that the max batch count and size values used in the function are consistent with the values entered in the Stream Analytics portal. 

Also, in a situation where there is no event landing in a time window, no output is generated. As a result, computeResult function will not be called. This behavior is consistent with the built-in windowed aggregate functions.


## Get help
For further assistance, try our [Azure Stream Analytics forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Next steps
You've been introduced to Stream Analytics, a managed service for streaming analytics on data from the Internet of Things. To learn more about this service, see:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
