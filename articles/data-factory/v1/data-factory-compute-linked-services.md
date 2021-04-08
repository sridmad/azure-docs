---
title: Compute environments supported by Azure Data Factory | Microsoft Docs
description: Learn about compute environments that you can use in Azure Data Factory pipelines (such as Azure HDInsight) to transform or process data.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: monicar

ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo

robots: noindex
---
# Compute environments supported by Azure Data Factory
> [!NOTE]
> This article applies to version 1 of Data Factory, which is generally available (GA). If you are using version 2 of the Data Factory service, which is in preview, see [Compute linked services in V2](../compute-linked-services.md).

This article explains different compute environments that you can use to process or transform data. It also provides details about different configurations (on-demand vs. bring your own) supported by Data Factory when configuring linked services linking these compute environments to an Azure data factory.

The following table provides a list of compute environments supported by Data Factory and the activities that can run on them. 

| Compute environment                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [On-demand HDInsight cluster](#azure-hdinsight-on-demand-linked-service) or [your own HDInsight cluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning activities: Batch Execution and Update Resource](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Stored Procedure](data-factory-stored-proc-activity.md) |

## Supported HDInsight versions in Azure Data Factory
Azure HDInsight supports multiple Hadoop cluster versions that can be deployed at any time. Each version choice creates a specific version of the Hortonworks Data Platform (HDP) distribution and a set of components that are contained within that distribution. Microsoft keeps updating the list of supported versions of HDInsight to provide latest Hadoop ecosystem components and fixes. For detailed information, see [supported HDInsight versions](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> The Linux-based HDInsight version 3.3 was retired on July 31, 2017. Data Factory v1 On-Demand HDInsight Linked Services customers were given until December 15, 2017, to test and upgrade to a later version of HDInsight. The Windows-based HDInsight will be retired on July 31, 2018.
>
> 

**What will happen after retirement date** 

After December 15, 2017:

- You will no longer be able to create Linux-based HDInsight version 3.3 (or earlier versions) clusters using On-Demand HDInsight Linked Service in Azure Data Factory v1. 

- If the  [osType and/or Version property](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) are not explicitly specified in existing Azure Data Factory v1 On-Demand HDInsight Linked Service JSON definitions, the default value will be changed from **Version=3.1, osType=Windows** to **Version=3.6, osType=Linux**.

After July 31, 2018:

- You will no longer be able to create any version of Windows-based HDInsight clusters using On-Demand HDInsight Linked Service in Azure Data Factory v1. 

 **Recommended actions** 

- Update the [osType and/or Version property](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) of the impacted Azure Data Factory v1 On-Demand HDInsight Linked Service definitions to newer Linux-based HDInsight versions (HDInsight 3.6) to make sure you can use the latest Hadoop ecosystem components and fixes. 
- Before December 15, 2017, test Azure Data Factory V1 Hive, Pig, MapReduce, and Hadoop streaming activities that reference the impacted Linked Service to make sure they are compatible with the new *osType* and/or *Version* default value (Version=3.6, osType=Linux) or the explicit HDInsight version and osType you are upgrading to. To learn more about compatibility, please review the [Migrate     from a Windows-based HDInsight cluster to a Linux-based cluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) and [What are the Hadoop components and versions available with     HDInsight?](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) documentation webpages. 
- Explicitly set the osType to Windows before December 15, 2017, if you would like to continue using Azure Data Factory v1On-Demand HDInsight Linked Service to create Windows-based HDInsight clusters. However, we still recommend migrating to Linux-based HDInsight clusters before July 31, 2018. 
- Update the DotNet Custom Activity JSON definition to use an Azure Batch Linked Service instead, if you are using On-Demand HDInsight Linked Service to execute Azure Data Factory v1DotNet Custom Activity. Learn more on the [Use custom activities in an Azure DataFactory pipeline](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-use-custom-activities) documentation webpage. 

>[!Note]
>For customers who use your existing Bring Your Own Cluster (BYOC) HDInsight Linked Service in Azure Data Factory v1 or those who are using BYOC and On-Demand HDInsightLinked Service in Azure Data Factory v2, the latest version support policy ofAzure HDInsight clusters is already enforced, therefore, no action is required. 
>
> 


## On-demand compute environment
In this type of configuration, the computing environment is fully managed by the Azure Data Factory service. It is automatically created by the Data Factory service before a job is submitted to process data and removed when the job is completed. You can create a linked service for the on-demand compute environment, configure it, and control granular settings for job execution, cluster management, and bootstrapping actions.

> [!NOTE]
> The on-demand configuration is currently supported only for Azure HDInsight clusters.
>
> 

## Azure HDInsight On-Demand Linked Service
The Azure Data Factory service can automatically create a Windows/Linux-based on-demand HDInsight cluster to process data. The cluster is created in the same region as the storage account (linkedServiceName property in the JSON) associated with the cluster.

Note the following **important** points about on-demand HDInsight linked service:

* You do not see the on-demand HDInsight cluster created in your Azure subscription. the Azure Data Factory service manages the on-demand HDInsight cluster on your behalf.
* The logs for jobs that are run on an on-demand HDInsight cluster are copied to the storage account associated with the HDInsight cluster. You can access these logs from the Azure portal in the **Activity Run Details** blade. See [Monitor and Manage Pipelines](data-factory-monitor-manage-pipelines.md) article for details.
* You are charged only for the time when the HDInsight cluster is up and running jobs.

> [!IMPORTANT]
> It typically takes **20 minutes** or more to provision an Azure HDInsight cluster on demand.
>
> 

### Example
The following JSON defines a Linux-based on-demand HDInsight linked service. The Data Factory service automatically creates a **Linux-based** HDInsight cluster when processing a data slice. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

To use a Windows-based HDInsight cluster, set **osType** to **windows** or do not use the property as the default value is: windows.  

> [!IMPORTANT]
> The HDInsight cluster creates a **default container** in the blob storage you specified in the JSON (**linkedServiceName**). HDInsight does not delete this container when the cluster is deleted. This behavior is by design. With on-demand HDInsight linked service, a HDInsight cluster is created every time a slice needs to be processed unless there is an existing live cluster (**timeToLive**) and is deleted when the processing is done. 
>
> As more slices are processed, you see many containers in your Azure blob storage. If you do not need them for troubleshooting of the jobs, you may want to delete them to reduce the storage cost. The names of these containers follow a pattern: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Use tools such as [Microsoft Storage Explorer](http://storageexplorer.com/) to delete containers in your Azure blob storage.
>
> 

### Properties
| Property                     | Description                              | Required |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | The type property should be set to **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Number of worker/data nodes in the cluster. The HDInsight cluster is created with 2 head nodes along with the number of worker nodes you specify for this property. The nodes are of size Standard_D3 that has 4 cores, so a 4 worker node cluster takes 24 cores (4\*4 = 16 cores for worker nodes, plus 2\*4 = 8 cores for head nodes). See [Create Linux-based Hadoop clusters in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) for details about the Standard_D3 tier. | Yes      |
| timetolive                   | The allowed idle time for the on-demand HDInsight cluster. Specifies how long the on-demand HDInsight cluster stays alive after completion of an activity run if there are no other active jobs in the cluster.<br/><br/>For example, if an activity run takes 6 minutes and timetolive is set to 5 minutes, the cluster stays alive for 5 minutes after the 6 minutes of processing the activity run. If another activity run is executed with the 6-minutes window, it is processed by the same cluster.<br/><br/>Creating an on-demand HDInsight cluster is an expensive operation (could take a while), so use this setting as needed to improve performance of a data factory by reusing an on-demand HDInsight cluster.<br/><br/>If you set timetolive value to 0, the cluster is deleted as soon as the activity run completes. Whereas, if you set a high value, the cluster may stay idle unnecessarily resulting in high costs. Therefore, it is important that you set the appropriate value based on your needs.<br/><br/>If the timetolive property value is appropriately set, multiple pipelines can share the instance of the on-demand HDInsight cluster. | Yes      |
| version                      | Version of the HDInsight cluster. The default value is 3.1 for Windows cluster and 3.2 for Linux cluster. | No       |
| linkedServiceName            | Azure Storage linked service to be used by the on-demand cluster for storing and processing data. The HDInsight cluster is created in the same region as this Azure Storage account.<p>Currently, you cannot create an on-demand HDInsight cluster that uses an Azure Data Lake Store as the storage. If you want to store the result data from HDInsight processing in an Azure Data Lake Store, use a Copy Activity to copy the data from the Azure Blob Storage to the Azure Data Lake Store. </p> | Yes      |
| additionalLinkedServiceNames | Specifies additional storage accounts for the HDInsight linked service so that the Data Factory service can register them on your behalf. These storage accounts must be in the same region as the HDInsight cluster, which is created in the same region as the storage account specified by linkedServiceName. | No       |
| osType                       | Type of operating system. Allowed values are: Windows (default) and Linux | No       |
| hcatalogLinkedServiceName    | The name of Azure SQL linked service that point to the HCatalog database. The on-demand HDInsight cluster is created by using the Azure SQL database as the metastore. | No       |

#### additionalLinkedServiceNames JSON example

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### Advanced Properties
You can also specify the following properties for the granular configuration of the on-demand HDInsight cluster.

| Property               | Description                              | Required |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Specifies the core configuration parameters (as in core-site.xml) for the HDInsight cluster to be created. | No       |
| hBaseConfiguration     | Specifies the HBase configuration parameters (hbase-site.xml) for the HDInsight cluster. | No       |
| hdfsConfiguration      | Specifies the HDFS configuration parameters (hdfs-site.xml) for the HDInsight cluster. | No       |
| hiveConfiguration      | Specifies the hive configuration parameters (hive-site.xml) for the HDInsight cluster. | No       |
| mapReduceConfiguration | Specifies the MapReduce configuration parameters (mapred-site.xml) for the HDInsight cluster. | No       |
| oozieConfiguration     | Specifies the Oozie configuration parameters (oozie-site.xml) for the HDInsight cluster. | No       |
| stormConfiguration     | Specifies the Storm configuration parameters (storm-site.xml) for the HDInsight cluster. | No       |
| yarnConfiguration      | Specifies the Yarn configuration parameters (yarn-site.xml) for the HDInsight cluster. | No       |

#### Example – On-demand HDInsight cluster configuration with advanced properties

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### Node sizes
You can specify the sizes of head, data, and zookeeper nodes using the following properties: 

| Property          | Description                              | Required |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Specifies the size of the head node. The default value is: Standard_D3. See the **Specifying node sizes** section for details. | No       |
| dataNodeSize      | Specifies the size of the data node. The default value is: Standard_D3. | No       |
| zookeeperNodeSize | Specifies the size of the Zoo Keeper node. The default value is: Standard_D3. | No       |

#### Specifying node sizes
See the [Sizes of Virtual Machines](../../virtual-machines/linux/sizes.md) article for string values you need to specify for the properties mentioned in the previous section. The values need to conform to the **CMDLETs & APIS** referenced in the article. As you can see in the article, the data node of Large (default) size has 7-GB memory, which may not be good enough for your scenario. 

If you want to create D4 sized head nodes and worker nodes, specify **Standard_D4** as the value for headNodeSize and dataNodeSize properties. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

If you specify a wrong value for these properties, you may receive the following **error:** Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'PreClusterCreationValidationFailure'. When you receive this error, ensure that you are using the **CMDLET & APIS** name from the table in the [Sizes of Virtual Machines](../../virtual-machines/linux/sizes.md) article.  

> [!NOTE]
> Currently Azure Data Factory does not support HDInsight clusters using Azure Data Lake Store as primary store. Use Azure Storage as primary store for HDInsight clusters. 
>
> 


## Bring your own compute environment
In this type of configuration, users can register an already existing computing environment as a linked service in Data Factory. The computing environment is managed by the user and the Data Factory service uses it to execute the activities.

This type of configuration is supported for the following compute environments:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## Azure HDInsight Linked Service
You can create an Azure HDInsight linked service to register your own HDInsight cluster with Data Factory.

### Example

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### Properties
| Property          | Description                              | Required |
| ----------------- | ---------------------------------------- | -------- |
| type              | The type property should be set to **HDInsight**. | Yes      |
| clusterUri        | The URI of the HDInsight cluster.        | Yes      |
| username          | Specify the name of the user to be used to connect to an existing HDInsight cluster. | Yes      |
| password          | Specify password for the user account.   | Yes      |
| linkedServiceName | Name of the Azure Storage linked service that refers to the Azure blob storage used by the HDInsight cluster. <p>Currently, you cannot specify an Azure Data Lake Store linked service for this property. If the HDInsight cluster has access to the Data Lake Store, you may access data in the Azure Data Lake Store from Hive/Pig scripts. </p> | Yes      |

## Azure Batch Linked Service
You can create an Azure Batch linked service to register a Batch pool of virtual machines (VMs) to a data factory. You can run .NET custom activities using either Azure Batch or Azure HDInsight.

See following topics if you are new to Azure Batch service:

* [Azure Batch basics](../../batch/batch-technical-overview.md) for an overview of the Azure Batch service.
* [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet to create an Azure Batch account (or) [Azure portal](../../batch/batch-account-create-portal.md) to create the Azure Batch account using Azure portal. See [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) topic for detailed instructions on using the cmdlet.
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet to create an Azure Batch pool.

### Example

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Append "**.\<region name\>**" to the name of your batch account for the **accountName** property. Example:

```json
"accountName": "mybatchaccount.eastus"
```

Another option is to provide the batchUri endpoint as shown in the following sample:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### Properties
| Property          | Description                              | Required |
| ----------------- | ---------------------------------------- | -------- |
| type              | The type property should be set to **AzureBatch**. | Yes      |
| accountName       | Name of the Azure Batch account.         | Yes      |
| accessKey         | Access key for the Azure Batch account.  | Yes      |
| poolName          | Name of the pool of virtual machines.    | Yes      |
| linkedServiceName | Name of the Azure Storage linked service associated with this Azure Batch linked service. This linked service is used for staging files required to run the activity and storing the activity execution logs. | Yes      |

## Azure Machine Learning Linked Service
You create an Azure Machine Learning linked service to register a Machine Learning batch scoring endpoint to a data factory.

### Example

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### Properties
| Property   | Description                              | Required |
| ---------- | ---------------------------------------- | -------- |
| Type       | The type property should be set to: **AzureML**. | Yes      |
| mlEndpoint | The batch scoring URL.                   | Yes      |
| apiKey     | The published workspace model’s API.     | Yes      |

## Azure Data Lake Analytics Linked Service
You create an **Azure Data Lake Analytics** linked service to link an Azure Data Lake Analytics compute service to an Azure data factory. The Data Lake Analytics U-SQL activity in the pipeline refers to this linked service. 

The following table provides descriptions for the generic properties used in the JSON definition. You can further choose between service principal and user credential authentication.

| Property                 | Description                              | Required                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | The type property should be set to: **AzureDataLakeAnalytics**. | Yes                                      |
| **accountName**          | Azure Data Lake Analytics Account Name.  | Yes                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | No                                       |
| **subscriptionId**       | Azure subscription id                    | No (If not specified, subscription of the data factory is used). |
| **resourceGroupName**    | Azure resource group name                | No (If not specified, resource group of the data factory is used). |

### Service principal authentication (recommended)
To use service principal authentication, register an application entity in Azure Active Directory (Azure AD) and grant it the access to Data Lake Store. For detailed steps, see [Service-to-service authentication](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Make note of the following values, which you use to define the linked service:
* Application ID
* Application key 
* Tenant ID

Use service principal authentication by specifying the following properties:

| Property                | Description                              | Required |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Specify the application's client ID.     | Yes      |
| **servicePrincipalKey** | Specify the application's key.           | Yes      |
| **tenant**              | Specify the tenant information (domain name or tenant ID) under which your application resides. You can retrieve it by hovering the mouse in the upper-right corner of the Azure portal. | Yes      |

**Example: Service principal authentication**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### User credential authentication
Alternatively, you can use user credential authentication for Data Lake Analytics by specifying the following properties:

| Property          | Description                              | Required |
| :---------------- | :--------------------------------------- | :------- |
| **authorization** | Click the **Authorize** button in the Data Factory Editor and enter your credential that assigns the autogenerated authorization URL to this property. | Yes      |
| **sessionId**     | OAuth session ID from the OAuth authorization session. Each session ID is unique and can be used only once. This setting is automatically generated when you use the Data Factory Editor. | Yes      |

**Example: User credential authentication**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### Token expiration
The authorization code you generated by using the **Authorize** button expires after sometime. See the following table for the expiration times for different types of user accounts. You may see the following error message when the authentication **token expires**: Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| User type                                | Expires after                            |
| :--------------------------------------- | :--------------------------------------- |
| User accounts NOT managed by Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 hours                                 |
| Users accounts managed by Azure Active Directory (AAD) | 14 days after the last slice run. <br/><br/>90 days, if a slice based on OAuth-based linked service runs at least once every 14 days. |

To avoid/resolve this error, reauthorize using the **Authorize** button when the **token expires** and redeploy the linked service. You can also generate values for **sessionId** and **authorization** properties programmatically using code as follows:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

See [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), and [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) topics for details about the Data Factory classes used in the code. Add a reference to: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll for the WindowsFormsWebAuthenticationDialog class. 

## Azure SQL Linked Service
You create an Azure SQL linked service and use it with the [Stored Procedure Activity](data-factory-stored-proc-activity.md) to invoke a stored procedure from a Data Factory pipeline. See [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties) article for details about this linked service.

## Azure SQL Data Warehouse Linked Service
You create an Azure SQL Data Warehouse linked service and use it with the [Stored Procedure Activity](data-factory-stored-proc-activity.md) to invoke a stored procedure from a Data Factory pipeline. See [Azure SQL Data Warehouse Connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) article for details about this linked service.

## SQL Server Linked Service
You create a SQL Server linked service and use it with the [Stored Procedure Activity](data-factory-stored-proc-activity.md) to invoke a stored procedure from a Data Factory pipeline. See [SQL Server connector](data-factory-sqlserver-connector.md#linked-service-properties) article for details about this linked service.

