---
title: Planning the Service Fabric cluster capacity | Microsoft Docs
description: Service Fabric cluster capacity planning considerations. Nodetypes, Operations, Durability and Reliability tiers
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''

ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: chackdan

---
# Service Fabric cluster capacity planning considerations
For any production deployment, capacity planning is an important step. Here are some of the items that you have to consider as a part of that process.

* The number of node types your cluster needs to start out with
* The properties of each of node type (size, primary, internet facing, number of VMs, etc.)
* The reliability and durability characteristics of the cluster

Let us briefly review each of these items.

## The number of node types your cluster needs to start out with
First, you need to figure out what the cluster you are creating is going to be used for and what kinds of applications you are planning to deploy into this cluster. If you are not clear on the purpose of the cluster, you are most likely not yet ready to enter the capacity planning process.

Establish the number of node types your cluster needs to start out with.  Each node type is mapped to a Virtual Machine Scale Set. Each node type can then be scaled up or down independently, have different sets of ports open, and can have different capacity metrics. So the decision of the number of node types essentially comes down to the following considerations:

* Does your application have multiple services, and do any of them need to be public or internet facing? Typical applications contain a front-end gateway service that receives input from a client, and one or more back-end services that communicate with the front-end services. So in this case, you end up having at least two node types.
* Do your services (that make up your application) have different infrastructure needs such as greater RAM or higher CPU cycles? For example, let us assume that the application that you want to deploy contains a front-end service and a back-end service. The front-end service can run on smaller VMs (VM sizes like D2) that have ports open to the internet.  The back-end service, however, is computation intensive and needs to run on larger VMs (with VM sizes like D4, D6, D15) that are not internet facing.
  
  In this example, although you can decide to put all the services on one node type, we recommended that you place them in a cluster with two node types.  This allows for each node type to have distinct properties such as internet connectivity or VM size. The number of VMs can be scaled independently, as well.  
* Since you cannot predict the future, go with facts you know of and decide on the number of node types that your applications need to start with. You can always add or remove node types later. A Service Fabric cluster must have at least one node type.

## The properties of each node type
The **node type** can be seen as equivalent to roles in Cloud Services. Node types define the VM sizes, the number of VMs, and their properties. Every node type that is defined in a Service Fabric cluster is set up as a separate virtual machine scale set. 
Virtual machine scale set is an Azure compute resource you can use to deploy and manage a collection of virtual machines as a set. Being defined as distinct virtual machine scale set, each node type can then be scaled up or down independently, have different sets of ports open, and can have different capacity metrics.

Read [this document](service-fabric-cluster-nodetypes.md) for more details on the relationship of Nodetypes to virtual machine scale set, how to RDP into one of the instances, open new ports etc.

Your cluster can have more than one node type, but the primary node type (the first one that you define on the portal) must have at least five VMs for clusters used for production workloads (or at least three VMs for test clusters). If you are creating the cluster using a Resource Manager template, then look for **is Primary** attribute under the node type definition. The primary node type is the node type where Service Fabric system services are placed.  

### Primary node type
For a cluster with multiple node types, you need to choose one of them to be primary. Here are the characteristics of a primary node type:

* The **minimum size of VMs** for the primary node type is determined by the **durability tier** you choose. The default for the durability tier is Bronze. Scroll down for details on what the durability tier is and the values it can take.  
* The **minimum number of VMs** for the primary node type is determined by the **reliability tier** you choose. The default for the reliability tier is Silver. Scroll down for details on what the reliability tier is and the values it can take. 


* The Service Fabric system services (for example, the Cluster Manager service or Image Store service) are placed on the primary node type and so the reliability and durability of the cluster is determined by the reliability tier value and durability tier value you select for the primary node type.

![Screen shot of a cluster that has two Node Types ][SystemServices]

### Non-primary node type
For a cluster with multiple node types, there is one primary node type and the rest of them are non-primary. Here are the characteristics of a non-primary node type:

* The minimum size of VMs for this node type is determined by the durability tier you choose. The default for the durability tier is Bronze. Scroll down for details on what the durability tier is and the values it can take.  
* The minimum number of VMs for this node type can be one. However you should choose this number based on the number of replicas of the application/services that you would like to run in this node type. The number of VMs in a node type can be increased after you have deployed the cluster.

## The durability characteristics of the cluster
The durability tier is used to indicate to the system the privileges that your VMs have with the underlying Azure infrastructure. In the primary node type, this privilege allows Service Fabric to pause any VM level infrastructure request (such as a VM reboot, VM reimage, or VM migration) that impact the quorum requirements for the system services and your stateful services. In the non-primary node types, this privilege allows Service Fabric to pause any VM level infrastructure requests like VM reboot, VM reimage, VM migration etc., that impact the quorum requirements for your stateful services running in it.

This privilege is expressed in the following values:

* Gold - The infrastructure Jobs can be paused for a duration of two hours per UD. Gold durability can be enabled only on full node VM skus like D15_V2, G5 etc.
* Silver - The infrastructure Jobs can be paused for a duration of 10 minutes per UD and is available on all standard VMs of single core and above.
* Bronze - No privileges. This is the default. Only use this durability level for Node Types that run _only_ stateless workloads. 

> [!WARNING]
> NodeTypes running with Bronze durability obtain _no privileges_. This means that infrastructure jobs that impact your stateless workloads will not be stopped or delayed. It is possible that such jobs can still impact your workloads, causing downtime or other issues. For any sort of production workload, running with at least Silver is recommended. You must maintain a minimum count of 5 nodes for any node-type that has a durability of Gold or Silver. 
> 

You get to choose durability level for each of your node-types.You can choose one node-type to have a durability level of Gold or silver and the other have Bronze in the same cluster.**You must maintain a minimum count of 5 nodes for any node-type that has a durability of Gold or silver**. 

**Advantages of using Silver or Gold durability levels**
 
1. Reduces the number of required steps in a scale-in operation (that is, node deactivation and Remove-ServiceFabricNodeState is called automatically)
2. Reduces the risk of data loss due to a customer-initiated in-place VM SKU change operation or Azure infrastructure operations.
	 
**Disadvantages of using Silver or Gold durability levels**
 
1. Deployments to your Virtual Machine Scale Set and other related Azure resources) can be delayed, can time out, or can be blocked entirely by problems in your cluster or at the infrastructure level. 
2. Increases the number of [replica lifecycle events](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle ) (for example, primary swaps) due to automated node deactivations during Azure infrastructure operations.

### Recommendations on when to use Silver or Gold durability levels

Use Silver or Gold durability for all node types that host stateful services you expect to scale-in (reduce VM instance count) frequently, and you would prefer that deployment operations be delayed in favor of simplifying these scale-in operations. The scale-out scenarios (adding VMs instances) do not play into your choice of the durability tier, only scale-in does.

### Changing durability levels
- Node types with durability levels of Silver or Gold cannot be downgraded to Bronze.
- Upgrading from Bronze to Silver or Gold can take a few hours.
- When changing durability level, be sure to update it in both the Service Fabric extension configuration in your VMSS resource, and in the node type definition in your Service Fabric cluster resource. These values must match.

### Operational Recommendations for the node type that you have set to silver or gold durability level.

1. Keep your cluster and applications healthy at all times, and make sure that applications respond to all [Service replica lifecycle events](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (like replica in build is stuck) in a timely fashion.
2. Adopt safer ways to make a VM SKU change (Scale up/down): Changing the VM SKU of a Virtual Machine Scale Set is inherently an unsafe operation and so should be avoided if possible. Here is the process you can follow to avoid common issues.
	- **For non-primary nodetypes:** It is recommended that you create new Virtual Machine Scale Set, modify the service placement constraint to include the new Virtual Machine Scale Set/node type and then reduce the old Virtual Machine Scale Set instance count to 0, one node at a time (this is to make sure that removal of the nodes do not impact the reliability of the cluster).
	- **For the primary nodetype:** Our recommendation is that you do not change VM SKU of the primary node type. Changing of the primary node type SKU is not supported. If the reason for the new SKU is capacity, we recommend adding more instances. If that not possible, create a new cluster and [restore application state](service-fabric-reliable-services-backup-restore.md) (if applicable) from your old cluster. You do not need to restore any system service state, they are recreated when you deploy your applications to your new cluster. If you were just running stateless applications on your cluster, then all you do is deploy your applications to the new cluster, you have nothing to restore. If you decide to go the unsupported route and want to change the VM SKU, then make modifications to the Virtual Machine Scale Set Model definition to reflect the new SKU. If your cluster has only one nodetype, then make sure that all your stateful applications respond to all [Service replica lifecycle events](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (like replica in build is stuck) in a timely fashion and that your service replica rebuild duration is less than five minutes (for Silver durability level). 


> [!WARNING]
> Changing the VM SKU Size for VM Scale Sets not running at least Silver durability is not recommened. Changing VM SKU Size is a data-destructive in-place infrastructure operation. Without at least some ability to delay or monitor this change, it is possible that the operation can cause dataloss for stateful services or cause other unforseen operational issues, even for stateless workloads. 
> 
	
3. Maintain a minimum count of five nodes for any Virtual Machine Scale Set that has durability level of Gold or Silver enabled
4. Do not delete random VM instances, always use Virtual Machine Scale Set scale down feature. The deletion of random VM instances has a potential of creating imbalances in the VM instance spread across UD and FD. This imbalance could adversely affect the systems ability to properly load balance amongst the service instances/Service replicas.
6. If using Autoscale, then set the rules such that scale in (removing of VM instances) are done only one node at a time. Scaling down more than one instance at a time is not safe.
7. If Scaling down a primary node type, you should never scale it down more than what the reliability tier allows.


## The reliability characteristics of the cluster
The reliability tier is used to set the number of replicas of the system services that you want to run in this cluster on the primary node type. The more the number of replicas, the more reliable the system services are in your cluster.  

The reliability tier can take the following values:

* Platinum - Run the System services with a target replica set count of 9
* Gold - Run the System services with a target replica set count of 7
* Silver - Run the System services with a target replica set count of 5 
* Bronze - Run the System services with a target replica set count of 3

> [!NOTE]
> The reliability tier you choose determines the minimum number of nodes your primary node type must have. 
> 
> 


### Recommendations for the reliability tier.

 When you increase or decrease the size of your cluster (the sum of VM instances in all node types), you must update the reliability of your cluster from one tier to another. Doing this triggers the cluster upgrades needed to change the system services replica set count. Wait for the upgrade in progress to complete before making any other changes to the cluster, like adding nodes.  You can monitor the progress of the upgrade on Service Fabric Explorer or by running [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Here is the recommendation on choosing the reliability tier.

| **Cluster Size** | **Reliability Tier** |
| --- | --- |
| 1 |Do not specify the Reliability Tier parameter, the system calculates it |
| 3 |Bronze |
| 5 or 6|Silver |
| 7 or 8 |Gold |
| 9 and up |Platinum |




## Primary node type - Capacity Guidance

Here is the guidance for planning the primary node type capacity

1. **Number of VM instances to run any production workload in Azure:** You must specify a minimum Primary Node type size of 5. 
2. **Number of VM instances to run test workloads in Azure** You can specify a minimum primary node type size of 1 or 3. The one node cluster, runs with a special configuration and so, scale out of that cluster is not supported. The one node cluster, has no reliability and so in your Resource Manager template, you have to remove/not specify that configuration (not setting the configuration value is not enough). If you set up the one node cluster set up via portal, then the configuration is automatically taken care of. 1 and 3 node clusters are not supported for running production workloads. 
3. **VM SKU:** Primary node type is where the system services run, so the VM SKU you choose for it, must take into account the overall peak load you plan to place into the cluster. Here is an analogy to illustrate what I mean here - Think of the primary node type as your "Lungs", it is what provides oxygen to your brain, and so if the brain does not get enough oxygen, your body suffers. 

Since the capacity needs of a cluster is determined by workload you plan to run in the cluster, we cannot provide you with qualitative guidance for your specific workload, however here is the broad guidance to help you get started

For production workloads 


- The recommended VM SKU is Standard D3 or Standard D3_V2 or equivalent with a minimum of 14 GB of local SSD.
- The minimum supported use VM SKU is Standard D1 or Standard D1_V2 or equivalent with a minimum of 14 GB of local SSD. 
- Partial core VM SKUs like Standard A0 are not supported for production workloads.
- Standard A1 SKU is not supported for production workloads for performance reasons.

> [!WARNING]
> Currently, changing the Primary node VM SKU size on a running cluster is not supported. So choose the primary node type VM SKU carefully, taking into account your capacity future needs. At this time, the only supported way to move your primary node type to a new VM SKU (smaller or larger) is to create a new cluster with the right capacity, deploy your applications to it and then restoring the application state (if applicable) from the [latest service backups](service-fabric-reliable-services-backup-restore.md) you have taken from the old cluster. You do not need to restore any system service state, they are recreated when you deploy applications to your new cluster. If you were just running stateless applications on your cluster, then all you do is deploy your applications to the new cluster, you have nothing to restore.
> 

## Non-Primary node type - Capacity Guidance for stateful workloads

This guidance is for stateful Workloads using Service fabric [reliable collections or reliable Actors](service-fabric-choose-framework.md) that you are running in the non-primary node type.


**Number of VM instances:** For production workloads that are stateful, it is recommended that you run them with a minimum and target replica count of 5. This means that in steady state you end up with a replica (from a replica set) in each fault domain and upgrade domain. The whole reliability tier concept for the primary node type is a way to specify this setting for system services. So the same consideration applies to your stateful services as well.

So for production workloads, the minimum recommended non-Primary Node type size is 5, if you are running stateful workloads in it.


**VM SKU:** This is the node type where your application services are running, so the VM SKU you choose for it, must take into account the peak load you plan to place into each Node. The capacity needs of the nodetype, is determined by workload you plan to run in the cluster, so we cannot provide you with qualitative guidance for your specific workload, however here is the broad guidance to help you get started

For production workloads 

- The recommended VM SKU is Standard D3 or Standard D3_V2 or equivalent with a minimum of 14 GB of local SSD.
- The minimum supported use VM SKU is Standard D1 or Standard D1_V2 or equivalent with a minimum of 14 GB of local SSD. 
- Partial core VM SKUs like Standard A0 are not supported for production workloads.
- Standard A1 SKU is specifically not supported for production workloads for performance reasons.


## Non-Primary node type - Capacity Guidance for stateless workloads

This guidance of stateless Workloads that you are running on the non-primary nodetype.

**Number of VM instances:** For production workloads that are stateless, the minimum supported non-Primary Node type size is 2. This allows you to run you two stateless instances of your application and allowing your service to survive the loss of a VM instance. 

> [!NOTE]
> If your cluster is running on a service fabric version less than 5.6, due to a defect in the runtime (this issue is fixed in 5.6), scaling down a non-primary node type to less than 5, results in cluster health turning unhealthy, till you call [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) with the appropriate node name. Read [perform Service Fabric cluster in or out](service-fabric-cluster-scale-up-down.md) for more details
> 
>

**VM SKU:** This is the node type where your application services are running, so the VM SKU you choose for it, must take into account the peak load you plan to place into each Node. The capacity needs of the nodetype, is determined by workload you plan to run in the cluster, So we cannot provide you with qualitative guidance for your specific workload, however here is the broad guidance to help you get started

For production workloads 


- The recommended VM SKU is Standard D3 or Standard D3_V2 or equivalent. 
- The minimum supported use VM SKU is Standard D1 or Standard D1_V2 or equivalent. 
- Partial core VM SKUs like Standard A0 are not supported for production workloads.
- Standard A1 SKU is not supported for production workloads for performance reasons.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Next steps
Once you finish your capacity planning and set up a cluster, read the following:

* [Service Fabric cluster security](service-fabric-cluster-security.md)
* [Disaster recovery planning](service-fabric-disaster-recovery.md)
* [Relationship of Nodetypes to Virtual machine scale set](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
