---
title: Azure SQL Data Sync (Preview) | Microsoft Docs
description: This overview introduces Azure SQL Data Sync (Preview)
services: sql-database
documentationcenter: ''
author: douglaslms
manager: craigg
editor: ''

ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: "On Demand"
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
---
# Sync data across multiple cloud and on-premises databases with SQL Data Sync (Preview)

SQL Data Sync is a service built on Azure SQL Database that lets you synchronize the data you select bi-directionally across multiple SQL databases and SQL Server instances.

Data Sync is based around the concept of a Sync Group. A Sync Group is a group of databases that you want to synchronize.

A Sync Group has the following properties:

-   The **Sync Schema** describes which data is being synchronized.

-   The **Sync Direction** can be bi-directional or can flow in only one direction. That is, the Sync Direction can be *Hub to Member* or *Member to Hub*, or both.

-   The **Sync Interval** is how often synchronization occurs.

-   The **Conflict Resolution Policy** is a group level policy, which can be *Hub wins* or *Member wins*.

Data Sync uses a hub and spoke topology to synchronize data. You define one of the databases in the group as the Hub Database. The rest of the databases are member databases. Sync occurs only between the Hub and individual members.
-   The **Hub Database** must be an Azure SQL Database.
-   The **member databases** can be either SQL Databases, on-premises SQL Server databases, or SQL Server instances on Azure virtual machines.
-   The **Sync Database** contains the metadata and log for Data Sync. The Sync Database has to be an Azure SQL Database located in the same region as the Hub Database. The Sync Database is customer created and customer owned.

> [!NOTE]
> If you're using an on premises database, you have to [configure a local agent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sync data between databases](media/sql-database-sync-data/sync-data-overview.png)

## When to use Data Sync

Data Sync is useful in cases where data needs to be kept up to date across several Azure SQL Databases or SQL Server databases. Here are the main use cases for Data Sync:

-   **Hybrid Data Synchronization:** With Data Sync, you can keep data synchronized between your on-premises databases and Azure SQL Databases to enable hybrid applications. This capability may appeal to customers who are considering moving to the cloud and would like to put some of their application in Azure.

-   **Distributed Applications:** In many cases, it's beneficial to separate different workloads across different databases. For example, if you have a large production database, but you also need to run a reporting or analytics workload on this data, it's helpful to have a second database for this additional workload. This approach minimizes the performance impact on your production workload. You can use Data Sync to keep these two databases synchronized.

-   **Globally Distributed Applications:** Many businesses span several regions and even several countries. To minimize network latency, it's best to have your data in a region close to you. With Data Sync, you can easily keep databases in regions around the world synchronized.

Data Sync is not appropriate for the following scenarios:

-   Disaster Recovery

-   Read Scale

-   ETL (OLTP to OLAP)

-   Migration from on-premises SQL Server to Azure SQL Database

## How does Data Sync work? 

-   **Tracking data changes:** Data Sync tracks changes using insert, update, and delete triggers. The changes are recorded in a side table in the user database.

-   **Synchronizing data:** Data Sync is designed in a Hub and Spoke model. The Hub syncs with each member individually. Changes from the Hub are downloaded to the member and then changes from the member are uploaded to the Hub.

-   **Resolving conflicts:** Data Sync provides two options for conflict resolution, *Hub wins* or *Member wins*.
    -   If you select *Hub wins*, the changes in the hub always overwrite changes in the member.
    -   If you select *Member wins*, the changes in the member overwrite changes in the hub. If there's more than one member, the final value depends on which member syncs first.

## <a name="sync-req-lim"></a> Requirements and limitations

### General considerations

#### Eventual consistency
Since Data Sync is trigger-based, transactional consistency is not guaranteed. Microsoft guarantees that all changes are made eventually and that Data Sync does not cause data loss.

#### Performance impact
Data Sync uses insert, update, and delete triggers to track changes. It creates side tables in the user database for change tracking. These change tracking activities have an impact on your database workload. Assess your service tier and upgrade if needed.

### General requirements

-   Each table must have a primary key. Don't change the value of the primary key in any row. If you have to change a primary key value, delete the row and recreate it with the new primary key value. 

-   Snapshot isolation must be enabled. For more info, see [Snapshot Isolation in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### General limitations

-   A table cannot have an identity column that is not the primary key.

-   The names of objects (databases, tables, and columns) cannot contain the printable characters period (.), left square bracket ([), or right square bracket (]).

-   Azure Active Directory authentication is not supported.

#### Unsupported data types

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (XML supported)

-   Cursor, Timestamp, Hierarchyid

#### Limitations on service and database dimensions

| **Dimensions**                                                      | **Limit**              | **Workaround**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximum number of sync groups any database can belong to.       | 5                      |                             |
| Maximum number of endpoints in a single sync group              | 30                     | Create multiple sync groups |
| Maximum number of on-premises endpoints in a single sync group. | 5                      | Create multiple sync groups |
| Database, table, schema, and column names                       | 50 characters per name |                             |
| Tables in a sync group                                          | 500                    | Create multiple sync groups |
| Columns in a table in a sync group                              | 1000                   |                             |
| Data row size on a table                                        | 24 Mb                  |                             |
| Minimum sync interval                                           | 5 Minutes              |                             |
|||

## FAQ about SQL Data Sync

### How much does the SQL Data Sync (Preview) service cost?

During the Preview, there is no charge for the SQL Data Sync (Preview) service itself.  However, you still accrue data transfer charges for data movement in and out of your SQL Database instance. For more info, see [SQL Database pricing](https://azure.microsoft.com/pricing/details/sql-database/).

### What regions support Data Sync?

SQL Data Sync (Preview) is available in all public cloud regions.

### Is a SQL Database account required? 

Yes. You must have a SQL Database account to host the Hub Database.

### Can I use Data Sync to sync between SQL Server on-premises databases only? 
Not directly. You can sync between SQL Server on-premises databases indirectly, however, by creating a Hub database in Azure, and then adding the on-premises databases to the sync group.
   
### Can I use Data Sync to seed data from my production database to an empty database, and then keep them synchronized? 
Yes. Create the schema manually in the new database by scripting it from the original. After you create the schema, add the tables to a sync group to copy the data and keep it synced.

### Should I use SQL Data Sync to back up and restore my databases?

It is not recommended to use SQL Data Sync (Preview) to create a backup of your data. You cannot back up and restore to a specific point in time because SQL Data Sync (Preview) synchronizations are not versioned. Furthermore, SQL Data Sync (Preview) does not back up other SQL objects, such as stored procedures, and does not do the equivalent of a restore operation quickly.

For one recommended backup technique, see [Copy an Azure SQL database](sql-database-copy.md).

### Is collation supported in SQL Data Sync?

Yes. SQL Data Sync supports collation in the following scenarios:

-   If the selected sync schema tables are not already in your hub or member databases, then when you deploy the sync group, the service automatically creates the corresponding tables and columns with the collation settings selected in the empty destination databases.

-   If the tables to be synced already exist in both your hub and member databases, SQL Data Sync requires that the primary key columns have the same collation between hub and member databases to successfully deploy the sync group. There are no collation restrictions on columns other than the primary key columns.

### Is federation supported in SQL Data Sync?

Federation Root Database can be used in the SQL Data Sync (Preview) Service without any limitation. You cannot add the Federated Database endpoint to the current version of SQL Data Sync (Preview).

## Next steps

For more info about SQL Data Sync, see:

-   [Set up Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Monitor Azure SQL Data Sync with OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Complete PowerShell examples that show how to configure SQL Data Sync:
    -   [Use PowerShell to sync between multiple Azure SQL databases](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Download the SQL Data Sync REST API documentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

For more info about SQL Database, see:

-   [SQL Database Overview](sql-database-technical-overview.md)
-   [Database Lifecycle Management](https://msdn.microsoft.com/library/jj907294.aspx)
