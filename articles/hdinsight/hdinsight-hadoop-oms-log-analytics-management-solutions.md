---
title: Add HDInsight cluster management solutions to Azure Log Analytics | Microsoft Docs
description: Learn how to use Azure Log Analytics to create custom views for HDInsight clusters.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme

---
# Add HDInsight cluster management solutions to Log Analytics

HDInsight provides cluster-specific management solutions that you can add for Azure Log Analytics. [Management solutions](../log-analytics/log-analytics-add-solutions.md) add functionality to [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), providing additional data and analysis tools to Log Analytics. These solutions collect important performance metrics from your HDInsight clusters and provide the tools to search the metrics. These solutions also provide visualizations and dashboards for most cluster types supported in HDInsight. By using the metrics that you collect with the solution, you can create custom monitoring rules and alerts. 

In this article, you learn how to add cluster-specific management solutions to an Operations Management Suite workspace.

## Prerequisites

* You must have configured an HDInsight cluster to use Azure Log Analytics. For instructions, see [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## Add cluster-specific management solutions

In this section, you add an HBase cluster management solution to an existing Operations Management Suite workspace.

1. Open an HDInsigt cluster in the Azure portal, click **Monitoring**, and then click **Open OMS Dashboard**.

    ![Open Operations Management Suite dashboard](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Open OMS dashboard")

1. In the OMS dashboard, click **Solutions Gallery** or the **View Designer** icon from the left pane.

    ![Add management solution in Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Add management solution in Operations Management Suite")

2. In the Solutions Gallery, click one of the following tiles:

    - HDInsight Hadoop Monitoring
    - HDInsight HBase Monitoring (Preview)
    - HDInsight Kafka Monitoring
    - HDInsight Storm Monitoring
    - HDInsight Spark Monitoring

3. In the next screen, click **Add**.  The following screenshot shows the Add button for the HBase Monitoring.

     ![Add HBase management solution](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Add HBase management solution")

4. You can see a tile on OMS dashboard for the HBase management solution. If the cluster you associated with Operations Management Suite (as part of the prerequisite for this article) is an HBase cluster, the tile shows the name of the cluster and the number of nodes in the cluster.

    ![HBase management solution added](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase management solution added")

## Next steps

* [Query Azure Log Analytics to monitor HDInsight clusters](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## See also

* [Working with Operations Management Suite Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Create alert rules in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
