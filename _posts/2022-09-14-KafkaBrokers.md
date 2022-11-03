---
title: Kafka Brokers
date: 2022-09-14
categories: [Kafka]
tags: [kafka, brokers, zookeeper]     # TAG names should always be lowercase
---

# Kafka Brokers

Kafka is composed of two different clusters, Brokers cluster and ZooKeeper cluster.
Note: There is an idea to replace ZooKeeper with own managed quorum.
Before starting the broker cluster the Zookeeper cluster needs to be running.

## Zookeeper
Zookeeper cluster is used for orchestration/management of the brokers in the Broker cluster. This implies that the brokers
require running zookeeper already running. Here is a simple
[docker-compose file](https://mgrabul.github.io/posts/KafkaWithDockerCompose/) containing one Zookeeper node and three
brokers. The brokers have the task to store the data produced by the producer clients and provide the data for consuming
to the consumer clients. When a consumer or a producer client wants to either store/produce data or consume data,
at start it will ask any of the cluster node for the address and port of the broker that will handle the data.
On the second step the consumer aether reads or writes the data to the broker.

## Broker
Is an instance that has the purpose to store the messages delivered from the produces and provide the
messages to the consumers. Messages are stored in *topics* that are divided into *partitions* and at the
end *partitions* are divided into *segments*. Topic is a logical concept, it is a logical place
where producers write and consumers read. Topics internally are made from one or more partitions. The partitions are
stored on more than one brokers. It could be that  the same portion can be copied on more than one broker, or different
partitions from the same topic are distributed between different brokers. Therefor
one topic can be divided between more brokers. While topic is a logical concept, partitions and segments
are physical folders and files accordingly, and are located on the broker file system.
**Brokers are instances that hold the partitions and the segments. Brokers are the 'database' of the kafka system.**
