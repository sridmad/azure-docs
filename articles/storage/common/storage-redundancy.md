---
title: Data replication in Azure Storage | Microsoft Docs
description: Data in your Microsoft Azure Storage account is replicated for durability and high availability. Replication options include locally redundant storage (LRS), zone-redundant storage (ZRS), geo-redundant storage (GRS), and read-access geo-redundant storage (RA-GRS).
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn

ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
---

# Azure Storage replication
The data in your Microsoft Azure storage account is always replicated to ensure durability and high availability. Replication copies your data, either within the same data center, or to a second data center, depending on which replication option you choose. Replication protects your data and preserves your application up-time in the event of transient hardware failures. If your data is replicated to a second data center, it's protected from a catastrophic failure in the primary location.

Replication ensures that your storage account meets the [Service-Level Agreement (SLA) for Storage](https://azure.microsoft.com/support/legal/sla/storage/) even in the face of failures. See the SLA for information about Azure Storage guarantees for durability and availability.

When you create a storage account, you can select one of the following replication options:

* [Locally redundant storage (LRS)](#locally-redundant-storage)
* [Zone-redundant storage (ZRS)](#zone-redundant-storage)
* [Geo-redundant storage (GRS)](#geo-redundant-storage)
* [Read-access geo-redundant storage (RA-GRS)](#read-access-geo-redundant-storage)

Read-access geo-redundant storage (RA-GRS) is the default option when you create a storage account.

The following table provides a quick overview of the differences between LRS, ZRS, GRS, and RA-GRS, while subsequent sections address each type of replication in more detail.

| Replication strategy | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data is replicated across multiple datacenters. |No |Yes |Yes |Yes |
| Data can be read from a secondary location as well as the primary location. |No |No |No |Yes |
| Number of copies of data maintained on separate nodes. |3 |3 |6 |6 |

See [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/) for pricing information for the different redundancy options.

> [!NOTE]
> Premium Storage supports only locally redundant storage (LRS). For information about Premium Storage, see [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads](../../virtual-machines/windows/premium-storage.md).
>

## Locally redundant storage
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## Zone-redundant storage
Zone-redundant storage (ZRS) replicates your data asynchronously across datacenters within one or two regions in addition to storing three replicas similar to LRS, thus providing higher durability than LRS. Data stored in ZRS is durable even if the primary datacenter is unavailable or unrecoverable.
Customers who plan to use ZRS should be aware that:

* ZRS is only available for block blobs in general-purpose storage accounts, and is supported only in storage service versions 2014-02-14 and later.
* Since asynchronous replication involves a delay, in the event of a local disaster it is possible that changes that have not yet been replicated to the secondary will be lost if the data cannot be recovered from the primary.
* The replica may not be available until Microsoft initiates failover to the secondary.
* ZRS accounts cannot be converted later to LRS or GRS. Similarly, an existing LRS or GRS account cannot be converted to a ZRS account.
* ZRS accounts do not have metrics or logging capability.

## Geo-redundant storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## Read-access geo-redundant storage
Read-access geo-redundant storage (RA-GRS) maximizes availability for your storage account, by providing read-only access to the data in the secondary location, in addition to the replication across two regions provided by GRS.

When you enable read-only access to your data in the secondary region, your data is available on a secondary endpoint, in addition to the primary endpoint for your storage account. The secondary endpoint is similar to the primary endpoint, but appends the suffix `–secondary` to the account name. For example, if your primary endpoint for the Blob service is `myaccount.blob.core.windows.net`, then your secondary endpoint is `myaccount-secondary.blob.core.windows.net`. The access keys for your storage account are the same for both the primary and secondary endpoints.

Considerations:

* Your application has to manage which endpoint it is interacting with when using RA-GRS.
* Since asynchronous replication involves a delay, in the event of a regional disaster it is possible that changes that have not yet been replicated to the secondary region will be lost if the data cannot be recovered from the primary region.
* If Microsoft initiates failover to the secondary region, you will have read and write access to that data after the failover has completed. For more information, please see [Disaster Recovery Guidance](../storage-disaster-recovery-guidance.md). 
* RA-GRS is intended for high-availability purposes. For scalability guidance, please review the [performance checklist](../storage-performance-checklist.md).

## Frequently asked questions

<a id="howtochange"></a>
#### 1. How can I change the geo replication type of my storage account?

   You can change the geo replication type of your storage account between LRS, GRS, and RA-GRS using the [Azure portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) or programmatically using one of our many Storage Client Libraries. 
Please note that ZRS accounts cannot be converted LRS or GRS. Similarly, an existing LRS or GRS account cannot be converted to a ZRS account.

<a id="changedowntime"></a>
#### 2. Will there be any down time if I change the replication type of my storage account?

   No, there won’t be any down time.

<a id="changecost"></a>
#### 3. Will there be any additional cost if I change the replication type of my storage account?

   Yes. If you change from LRS to GRS (or RA-GRS) for your storage account, it would incur an additional charge for egress involved in copying existing data from primary location to the secondary location. Once the initial data is copied there is no further additional egress charge for geo replicating the data from the primary to secondary location. The details for bandwidth charges can be found on the [Azure Storage Pricing page](https://azure.microsoft.com/pricing/details/storage/blobs/). 
If you change from GRS to LRS, there is no additional cost, but your data will be deleted from the secondary location.

<a id="ragrsbenefits"></a>
#### 4. How can RA-GRS help me?
   
   GRS storage provides replication of your data from a primary to a secondary region that is hundreds of miles away from the primary region. In such case, your data is durable even in the case of a complete regional outage or a disaster in which the primary region is not recoverable. RA-GRS storage includes this plus it adds the ability to read the data from the secondary location. For some ideas about how to leverage this ability, please refer to [Designing Highly Available Applications using RA-GRS storage](../storage-designing-ha-apps-with-ragrs.md). 

<a id="lastsynctime"></a>
#### 5. Is there a way for me to figure out how long it takes to replicate my data from the primary to the secondary region?
   
   If you are using RA-GRS storage, you can check the Last Sync Time of your storage account. Last Sync Time is a GMT date/time value; all primary writes before the Last Sync Time have been successfully written to the secondary location, which means they are available to be read from the secondary location. Primary writes after the Last Sync Time may or may not be available for reads yet. You can query this value using the [Azure portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), or programmatically using the REST API or one of the Storage Client Libraries. 

<a id="outage"></a>
#### 6. How can I switch to the secondary region if there is an outage in the primary region?
   
   Please refer to the article [What to do if an Azure Storage outage occurs](../storage-disaster-recovery-guidance.md) for more details.

<a id="rpo-rto"></a>
#### 7. What is the RPO and RTO with GRS?
   
   Recover Point Objective (RPO): In GRS and RA-GRS, the storage service asynchronously geo-replicates the data from the primary to the secondary location. If there is a major regional disaster and a failover has to be performed, then recent delta changes that have not been geo-replicated may be lost. The number of minutes of potential data lost is referred to as the RPO (which means the point in time to which data can be recovered). We typically have an RPO less than 15 minutes, although there is currently no SLA on how long geo-replication takes.

   Recovery Time Objective (RTO): This is a measure of how long it takes us to do the failover and get the storage account back online if we have to do a failover. The time to do the failover includes the following:
    * The time it takes us to investigate and determine if we can recover the data at the primary location or if we need to do a failover.
    * Failover the account by changing the primary DNS entries to point to the secondary location.

   We take the responsibility of preserving your data very seriously, so if there is any chance of recovering the data, we will hold off on doing the failover and focus on recovering the data in the primary location. In the future, we plan to provide an API to allow you to trigger a failover at an account level, which would then allow you to control the RTO yourself, but this is not available yet.
   
## Next steps
* [Designing highly available applications using RA-GRS Storage](../storage-designing-ha-apps-with-ragrs.md)
* [Azure Storage pricing](https://azure.microsoft.com/pricing/details/storage/)
* [About Azure storage accounts](../storage-create-storage-account.md)
* [Azure Storage scalability and performance targets](storage-scalability-targets.md)
* [Microsoft Azure Storage redundancy options and read access geo redundant storage ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: A highly available cloud storage service with strong consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

