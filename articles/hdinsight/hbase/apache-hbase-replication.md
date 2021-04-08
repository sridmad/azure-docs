---
title: Set up HBase cluster replication in Azure virtual networks | Microsoft Docs
description: Learn how to set up HBase replication from one HDInsight version to another for load balancing, high availability, zero-downtime migration and updates, and disaster recovery.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2017
ms.author: jgao

---
# Set up HBase cluster replication in Azure virtual networks

Learn how to set up HBase replication within a virtual network, or between two virtual networks in Azure.

Cluster replication uses a source-push methodology. An HBase cluster can be a source or a destination, or it can fulfill both roles at once. Replication is asynchronous. The goal of replication is eventual consistency. When the source receives an edit to a column family when replication is enabled, the edit is propagated to all destination clusters. When data is replicated from one cluster to another, the source cluster and all clusters that have already consumed the data are tracked, to prevent replication loops.

In this tutorial, you set up a source-destination replication. For other cluster topologies, see the [Apache HBase reference guide](http://hbase.apache.org/book.html#_cluster_replication).

The following are HBase replication usage cases for a single virtual network:

* Load balancing. For example, you can run scans or MapReduce jobs on the destination cluster, and ingest data on the source cluster.
* Adding high availability.
* Migrating data from one HBase cluster to another.
* Upgrading an Azure HDInsight cluster from one version to another.

The following are HBase replication usage cases for two virtual networks:

* Setting up disaster recovery.
* Load balancing and partitioning the application.
* Adding high availability.

You can replicate clusters by using [script action](../hdinsight-hadoop-customize-cluster-linux.md) scripts from [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## Prerequisites
Before you begin this tutorial, you must have an Azure subscription. See [Get an Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Set up the environments

You have three configuration options:

- Two HBase clusters in one Azure virtual network.
- Two HBase clusters in two different virtual networks in the same region.
- Two HBase clusters in two different virtual networks in two different regions (geo-replication).

To help you set up the environments, we have created some [Azure Resource Manager templates](../../azure-resource-manager/resource-group-overview.md). If you prefer to set up the environments by using other methods, see:

- [Create Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Create HBase clusters in Azure Virtual Network](apache-hbase-provision-vnet.md)

### Set up one virtual network

To create two HBase clusters in the same virtual network, select the following image. The template is stored at [Azure QuickStart templates](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### Set up two virtual networks in the same region

To create two virtual networks with virtual network peering and two HBase clusters in the same region, select the following image. The template is stored at [Azure QuickStart templates](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



This scenario requires [virtual network peering](../../virtual-network/virtual-network-peering-overview.md). The template enables virtual network peering.   

HBase replication uses IP addresses of the ZooKeeper VMs. You must set up static IP addresses for the destination HBase ZooKeeper nodes.

**To configure static IP addresses**

1. Sign in to the [Azure portal](https://portal.azure.com).
2. On the left menu, select **Resource Groups**.
3. Select the resource group that has the destination HBase cluster. This is the resource group that you specified when you used the Resource Manager template to create the environment. You can use the filter to narrow the list. You can see a list of resources that contain the two virtual networks.
4. Select the virtual network that contains the destination HBase cluster. For example, select **xxxx-vnet2**. Three devices with names that start with **nic-zookeepermode-** are listed. Those devices are the three ZooKeeper VMs.
5. Select one of the ZooKeeper VMs.
6. Select **IP configurations**.
7. In the list, select **ipConfig1**.
8. Select **Static**, and copy or write down the actual IP address. You need the IP address when you run the script action to enable replication.

  ![HDInsight HBase replication ZooKeeper static IP address](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repeat step 6 to set the static IP address for the other two ZooKeeper nodes.

For the cross-virtual network scenario, you must use the **-ip** switch when you call the `hdi_enable_replication.sh` script action.

### Set up two virtual networks in two different regions

To create two virtual networks in two different regions and the VPN connection between the VNets, click the following image. The template is stored in [Azure QuickStart Templates](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Some of the hard-coded values in the template:

**VNet 1**

| Property | Value |
|----------|-------|
| Location | West US |
| VNet name | &lt;ClusterNamePrevix>-vnet1 |
| Address space prefix | 10.1.0.0/16 |
| Subnet name | subnet 1 |
| Subnet prefix | 10.1.0.0/24 |
| Subnet (gateway) name | GatewaySubnet (can't be changed) |
| Subnet (gateway) prefix | 10.1.255.0/27 |
| Gateway name | vnet1gw |
| Gateway type | Vpn |
| Gateway VPN type | RouteBased |
| Gateway SKU | Basic |
| Gateway IP | vnet1gwip |
| Cluster Name | &lt;ClusterNamePrefix>1 |
| Cluster version | 3.6 |
| Cluster kind | hbase |
| Cluster worker node count | 2 |


**VNet 2**

| Property | Value |
|----------|-------|
| Location | East US |
| VNet name | &lt;ClusterNamePrevix>-vnet2 |
| Address space prefix | 10.2.0.0/16 |
| Subnet name | subnet 1 |
| Subnet prefix | 10.2.0.0/24 |
| Subnet (gateway) name | GatewaySubnet (can't be changed) |
| Subnet (gateway) prefix | 10.2.255.0/27 |
| Gateway name | vnet2gw |
| Gateway type | Vpn |
| Gateway VPN type | RouteBased |
| Gateway SKU | Basic |
| Gateway IP | vnet1gwip |
| Cluster Name | &lt;ClusterNamePrefix>2 |
| Cluster version | 3.6 |
| Cluster kind | hbase |
| Cluster worker node count | 2 |

HBase replication uses the IP addresses of the ZooKeeper VMs. You must set up static IP addresses for the destination HBase ZooKeeper nodes. To set up static IP, see [Set up two virtual networks in the same region](#set-up-two-virtual-networks-in-the-same-region) in this article.

For the cross-virtual network scenario, you must use the **-ip** switch when you call the `hdi_enable_replication.sh` script action.

## Load test data

When you replicate a cluster, you must specify the tables that you want to replicate. In this section, you load some data into the source cluster. In the next section, you will enable replication between the two clusters.

To create a **Contacts** table and insert some data in the table, follow the instructions at [HBase tutorial: Get started using Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md).

## Enable replication

The following steps describe how to call the script action script from the Azure portal. For information about running a script action by using Azure PowerShell and the Azure command-line tool (Azure CLI), see [Customize HDInsight clusters by using script action](../hdinsight-hadoop-customize-cluster-linux.md).

**To enable HBase replication from the Azure portal**

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Open the source HBase cluster.
3. In the cluster menu, select **Script Actions**.
4. At the top of the page, select **Submit New**.
5. Select or enter the following information:

  1. **Name**: Enter **Enable replication**.
  2. **Bash Script URL**: Enter **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Head**: Ensure this is selected. Clear the other node types.
  4. **Parameters**: The following sample parameters enable replication for all existing tables, and then copy all data from the source cluster to the destination cluster:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Use hostname instead of FQDN for both the source and destination cluster DNS name.

6. Select **Create**. The script can take a while to run, especially when you use the **-copydata** argument.

Required arguments:

|Name|Description|
|----|-----------|
|-s, --src-cluster | Specifies the DNS name of the source HBase cluster. For example: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Specifies the DNS name of the destination (replica) HBase cluster. For example: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Specifies the admin password for Ambari on the source HBase cluster. |
|-dp, --dst-ambari-password | Specifies the admin password for Ambari on the destination HBase cluster.|

Optional arguments:

|Name|Description|
|----|-----------|
|-su, --src-ambari-user | Specifies the admin user name for Ambari on the source HBase cluster. The default value is **admin**. |
|-du, --dst-ambari-user | Specifies the admin user name for Ambari on the destination HBase cluster. The default value is **admin**. |
|-t, --table-list | Specifies the tables to be replicated. For example: --table-list="table1;table2;table3". If you don't specify tables, all existing HBase tables are replicated.|
|-m, --machine | Specifies the head node where the script action runs. The value is either **hn1** or **hn0**. Because the **hn0** head node typically is busier, we recommend using **hn1**. Use this option when you're running the $0 script as a script action from the HDInsight portal or Azure PowerShell.|
|-ip | Required when you're enabling replication between two virtual networks. This argument acts as a switch to use the static IP addresses of ZooKeeper nodes from replica clusters instead of FQDN names. You must preconfigure the static IP addresses before you enable replication. |
|-cp, -copydata | Enables the migration of existing data on the tables where replication is enabled. |
|-rpm, -replicate-phoenix-meta | Enables replication on Phoenix system tables. <br><br>*Use this option with caution.* We recommend that you re-create Phoenix tables on replica clusters before you use this script. |
|-h, --help | Displays usage information. |

The `print_usage()` section of the [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) has a detailed explanation of parameters.

After the script action is successfully deployed, you can use SSH to connect to the destination HBase cluster, and then verify that the data has been replicated.

### Replication scenarios

The following list shows you some general usage cases and their parameter settings:

- **Enable replication on all tables between the two clusters**. This scenario does not require copying or migrating existing data in the tables, and it does not use Phoenix tables. Use the following parameters:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Enable replication on specific tables**. To enable replication on table1, table2, and table3, use the following parameters:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Enable replication on specific tables, and copy the existing data**. To enable replication on table1, table2, and table3, use the following parameters:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Enable replication on all tables, and replicate Phoenix metadata from source to destination**. Phoenix metadata replication is not perfect. Use it with caution. Use the following parameters:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## Copy and migrate data

There are two separate script action scripts available for copying or migrating data after replication is enabled:

- [Script for small tables](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tables that are a few gigabytes in size, and overall copy is expected to finish in less than one hour)

- [Script for large tables](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tables that are expected to take longer than one hour to copy)

You can follow the same procedure that's described in [Enable replication](#enable-replication) to call the script action. Use the following parameters:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

The `print_usage()` section of the [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) has a detailed description of parameters.

### Scenarios

- **Copy specific tables (test1, test2, and test3) for all rows edited until now (current time stamp)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Or:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copy specific tables with a specified time range**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## Disable replication

To disable replication, use another script action script from [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). You can follow the same procedure that's described in [Enable replication](#enable-replication) to call the script action. Use the following parameters:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

The `print_usage()` section of the [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) has a detailed explanation of parameters.

### Scenarios

- **Disable replication on all tables**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  or

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Disable replication on specified tables (table1, table2, and table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## Next steps

In this tutorial, you learned how to set up HBase replication within a virtual network, or between two virtual networks. To learn more about HDInsight and HBase, see these articles:

* [Get started with Apache HBase in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase overview](./apache-hbase-overview.md)
* [Create HBase clusters in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Analyze sensor data with Storm and HBase in HDInsight (Hadoop)](../storm/apache-storm-sensor-data-analysis.md)
