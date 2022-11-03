---
title: Kafka Topic
date: 2022-09-14
categories: [Kafka]
tags: [kafka, topic]     # TAG names should always be lowercase
---
# Kafka Topic

**Please note that in the following text, consume and read are synonyms in the context of Kafka topic as are write and produce.**

Is a logical concept that holds, and stores messages. Clients are storing and reading messages from a Kafka topic.
The topic is a non-concrete concept, topic is not a physical structure. Topic is an abstraction of the Kafka storage system,
similar to what a database table is in a relational database. One topic is usually fiscally distributed between different
[Brokers](https://mgrabul.github.io/posts/KafkaBrokers/) in fiscal blocks called *partitions* that are actually folders in the
broker's file system. Topics can also be backed up/copied several times on the brokers. The number of copies that a
topic has is called the replication factor. Topics are used by the client's applications, the producer, and consumer
applications. Application developers need to have an understanding of what a topic is and how to produce and consume
messages from a topic. For example, an API application might write/publish messages to Kafka topic or it might
read/consume messages from a Kafka topic. That is why it is said that Kafka topic is an abstraction of the Kafka
storage system. Applications that use Kafka are not aware, or interested in how data is stored in Kafka, they only know
how to read/consume or write/produce data/messages to the Kafka topic.

## Kafka topic, reading/consuming and writing/producing messages concept
Kafka messages are generally written and read in an ordered
way. The first message that is written is generally the first message that is read. Off-course the
application can ask to read a particular message skipping the “First in First out” concept. For example, consumers can only
read the last 100 messages, or they can start reading from the first message or only start reading the new upcoming
messages, without caring about the previous messages.

## Parallel and sequential message reading in Kafka topic
Kafka can read topic messages in parallel and sequentially. Parallel reading is possible if the messages are independent
by nature or business case, but it happens that some messages are required to be read sequentially, one after the other.
Let's say transactions from the user's bank accounts. The order of statements in a bank account must be sequential by its business nature and requirements.
These transactions must be ordered by date, if they are not, then the account status might be wrong so the reading of transaction messages for a client must be sequential. On the other hand, it can
happen that messages reading and writing can be parallelized. Let's take the example of calculating the account balance in two different bank accounts.
To achieve this parallel writing and reading a topic can be split into *partitions*. Every message within the topic’s partition will be consumed or produced sequentially
but messages from two or more partitions can be consumed and or produced in parallel. Therefore in conclusion, if a system with sequential read and write needs to be developed then a topic with one partition needs to be created if a parallel system is required then a topic can be split into partitions that can work in parallel. Partitions are the building blocks of topics and they are physically present on the [Brokers](https://mgrabul.github.io/posts/KafkaBrokers/). Important is that,
*Reading and writing on different topics is always independent*

## Kafka topic, important commands

Create a topic hello_world:
```bash
kafka-topics --create --topic hello_world --partitions 3 --replication-factor 1 --bootstrap-server localhost:9092
```
Send message to hello_world:
```bash
kafka-console-producer --topic hello_world --broker-list localhost:9092
```

## Key takeaway

Kafka topic is an abstraction of the Kafka storage system.
Kafka topic is physically divided into partitions located on the same or different [broker](https://mgrabul.github.io/posts/KafkaBrokers/). Partitions are folders in the
[broker](https://mgrabul.github.io/posts/KafkaBrokers/) file system that message data in files called segments.
Kafka topic has a replication factor, that is the number of copies of that topic in the Kafka system or cluster.
Messages in the same partition are produced and consumed sequentially, but independently between different partitions
of the same topic.
