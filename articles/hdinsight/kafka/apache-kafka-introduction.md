---
title: An introduction to Apache Kafka on HDInsight - Azure | Microsoft Docs
description: 'Learn about Apache Kafka on HDInsight: What it is, what it does, and where to find examples and getting started information.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
---
# Introducing Apache Kafka on HDInsight

[Apache Kafka](https://kafka.apache.org) is an open-source distributed streaming platform that can be used to build real-time streaming data pipelines and applications. Kafka also provides message broker functionality similar to a message queue, where you can publish and subscribe to named data streams. Kafka on HDInsight provides you with a managed, highly scalable, and highly available service in the Microsoft Azure cloud.

## Why use Kafka on HDInsight?

Kafka on HDInsight provides the following features:

* Service Level Agreement (SLA): [SLA information for HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Publish-subscribe messaging pattern: Kafka provides a Producer API for publishing records to a Kafka topic. The Consumer API is used when subscribing to a topic.

* Stream processing: Kafka is often used with Apache Storm or Spark for real-time stream processing. Kafka 0.10.0.0 (HDInsight version 3.5 and 3.6) introduced a streaming API that allows you to build streaming solutions without requiring Storm or Spark.

* Horizontal scale: Kafka partitions streams across the nodes in the HDInsight cluster. Consumer processes can be associated with individual partitions to provide load balancing when consuming records.

* In-order delivery: Within each partition, records are stored in the stream in the order that they were received. By associating one consumer process per partition, you can guarantee that records are processed in-order.

* Fault-tolerant: Partitions can be replicated between nodes to provide fault tolerance.

* Integration with Azure Managed Disks: Managed disks provide higher scale and throughput for the disks used by the virtual machines in the HDInsight cluster.

    Managed disks are enabled by default for Kafka on HDInsight. The number of disks used per node can be configured during HDInsight creation. For more information on managed disks, see [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

    For information on configuring managed disks with Kafka on HDInsight, see [Increase scalability of Kafka on HDInsight](apache-kafka-scalability.md).

## Use cases

* **Messaging**: Since it supports the publish-subscribe message pattern, Kafka is often used as a message broker.

* **Activity tracking**: Since Kafka provides in-order logging of records, it can be used to track and re-create activities. For example, user actions on a web site or within an application.

* **Aggregation**: Using stream processing, you can aggregate information from different streams to combine and centralize the information into operational data.

* **Transformation**: Using stream processing, you can combine and enrich data from multiple input topics into one or more output topics.

## Architecture

![Kafka cluster configuration](./media/apache-kafka-introduction/kafka-cluster.png)

This diagram shows a typical Kafka configuration that uses consumer groups, partitioning, and replication to offer parallel reading of events with fault tolerance. Apache ZooKeeper is built for concurrent, resilient, and low-latency transactions, as it manages the state of the Kafka cluster. Kafka stores records in *topics*. Records are produced by *producers*, and consumed by *consumers*. Producers retrieve records from Kafka *brokers*. Each worker node in your HDInsight cluster is a Kafka broker. One partition is created for each consumer, allowing parallel processing of the streaming data. Replication is employed to spread the partitions across nodes, protecting against node (broker) outages. A partition denoted with an *(L)* is the leader for the given partition. Producer traffic is routed to the leader of each node, using the state managed by ZooKeeper.

> [!IMPORTANT]
> Kafka is not aware of the underlying hardware (rack) in the Azure data center. To ensure that partitions are correctly balanced across the underlying hardware, see [configure high availability of data (Kafka)](apache-kafka-high-availability.md).

## Next steps

Use the following links to learn how to use Apache Kafka on HDInsight:

* [Get started with Kafka on HDInsight](apache-kafka-get-started.md)

* [Use MirrorMaker to create a replica of Kafka on HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)