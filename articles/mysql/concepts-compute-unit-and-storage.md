---
title: 'Explaining Compute Units in Azure Database for MySQL | Microsoft Docs'
description: 'Azure DB for MySQL: This article explains the concepts of Compute Units and what happens when your workload reaches the maximum Compute Units.'
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
---
# Explaining Compute Units in Azure Database for MySQL
This topic explains the concept of Compute Units and what happens when your workload reaches the maximum Compute Units.

## What are Compute Units?
Compute Units are a measure of CPU processing throughput that is guaranteed to be available to a single Azure Database for MySQL server. A Compute Unit is a blended measure of CPU and memory resources. In general, 50 Compute Units equate to half of a core. 100 Compute Units equate to one core. 2,000 Compute Units equate to 20 cores of guaranteed processing throughput available to your server.

The amount of memory per Compute Unit is optimized for the Basic and Standard pricing tiers. Doubling the Compute Units by increasing the performance level equates to doubling the set of resource available to that single Azure Database for MySQL.

For example, a Standard 800 Compute Units provides 8x more CPU throughput and memory than does a Standard 100 Compute Units configuration. However, while Standard 100 Compute Units provide the same CPU throughput compared to Basic 100 Compute Units, the amount of memory that is pre-configured in the Standard pricing tier is double the amount of memory configured for the Basic pricing tier. Therefore, the Standard pricing tier provides better workload performance and lower transaction latency than does the Basic pricing tier with the same Compute Units selected.

## How can I determine the number of Compute Units needed for my workload?
If you want to migrate an existing MySQL server running on-premises or on a virtual machine, you can determine the number of Compute Units by estimating how many cores of processing throughput your workload requires. 

If your existing on-premises or virtual machine server is currently using 4 cores (without counting CPU hyperthread), start by configuring 400 Compute Units for your Azure Database for MySQL server. Compute Units can be scaled up or down dynamically depending on your workload needs, and with virtually no application downtime. 

Monitor the Metrics graph in the Azure portal or write Azure CLI commands to measure Compute Units. Relevant metrics to monitor are the Compute Unit percentage and Compute Unit limit.

>[!IMPORTANT]
> If you find storage IOPS are not fully utilized to the maximum, consider monitoring the Compute Units utilization as well. Raising the Compute Units may allow for higher IO throughput by lessening the performance bottleneck resulting from limited CPU or memory.

## What happens when I hit my maximum Compute Units?
Performance levels are calibrated and governed to provide resources to run your database workload up to the max limits for the selected pricing tier and performance level. 

If your workload reaches the maximum limits in either the Compute Units or provisioned IOPS limits, you can continue to use the resources at the maximum allowed level, but your queries are likely to encounter increased latencies. These limits result in a slowdown in the workload rather than errors, unless the slowdown becomes so severe that queries time out. 

If your workload reaches the maximum limits on number of connections, explicit errors are raised. For more information on resources limits, see [Limitations in Azure Database for MySQL](concepts-limits.md).

## Next steps
For more information on pricing tiers, see [Azure Database for MySQL pricing tiers](./concepts-service-tiers.md).
