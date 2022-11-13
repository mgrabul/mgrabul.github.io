---
title: Kafka Consumers in Spring Boot
date: 2022-11-03
categories: [Kafka]
tags: [kafka, consumers]     # TAG names should always be lowercase
---

# Kafka Consumers

Spring boot creates Kafka consumers by adding annotation on  `@KafkaListener`, simple example of a consumer is:

```java

@Component
public class SimpleKafkaConsumer1 {

 private static final Logger LOGGER = LoggerFactory.getLogger(SimpleKafkaConsumer1.class);

 private CountDownLatch latch = new CountDownLatch(1);
 private String payload;


 @KafkaListener(
   topics = AlertKafkaProducer.POJO_TOPIC,
   groupId = "2"
 )
 public void receiveAlertDTO(ConsumerRecord<String, AlertDTO> consumerRecord) {
   LOGGER.info("received receiveAlertDTO='{}', groupId = \"2\"", consumerRecord.toString());
   payload = consumerRecord.toString();
   latch.countDown();
 }
}
```

The current code will create a bean and will reweave messages that are objects from some class `AlertDTO`.
The `KafkaListener` annotation also specifies two params, a groupId and a topic. The topic is equivalent to DB table
in a relational database like MySQL, it is Kafka's logical unit for storing and retreating data. Messages are
published and consumed inside a topic. The "GroupId" concept does not exist in relational databases. It groups together more
consumers to allow parallel consumption of the topic's messages. The level of parallelism in the consumption of the topic's
messages is controlled by the number of partitions the topic has. Partitions for a topic are defined when a topic is
created. Every partition is assigned to a consumer inside every consumer's group. If a consumer group has one
consumer then all the partitions in the topic will be assigned to that consumer. If there are more consumers in a
consumer group then the partitions of a topic will be shared between the consumers. In any case, one partition can't have
more than one consumer assigned. If there are more consumers than partitions, some consumers will be idle, but all
partitions will have a consumer assigned. Once a message is consumed or in other words, once a consumer reads a message,
the action of consumption is recorded and Kafka holds a record of the consumed messages for every consumer in every
consumer group.
Going back to java-spring, more beans can be created to create more consumes or also more listeners in the same bean.
If not specified explicitly consumers are randomly assigned to partitions. It is essential to know that consumers will not
distinguish between the object that they need to consume.
The following examples define two consumers. The first one will consume the record from type "String" and the second one from
"SomeObject".
```java
@KafkaListener(
topics = "POJO_TOPIC",
groupId = "2"
)
public void receiveString(ConsumerRecord<String, String> consumerRecord) {
   LOGGER.info("received receiveAlertDTO='{}', groupId = \"2\"", consumerRecord.toString());
}
```

```java
@KafkaListener(
topics = "POJO_TOPIC",
groupId = "2"
)
public void receiveSomeObject(ConsumerRecord<String, SomeObject> consumerRecord) {
   LOGGER.info("received receiveAlertDTO='{}', groupId = \"2\"", consumerRecord.toString());
}
```
*!!This will not work!!**. Consumers are not assigned to the type of objects they consume!

## Key takeaway
* Consumers are organized in consumer groups.
* Consumers from different groups are reading the topic independently and in parallel.
* Consumers from same group read the topic in parallel only if they are assigned to different partitions.
* Kafka keeps track of what consumers have already read, this value is called consumer group offset.
* Spring does not assign consumers on the type of data they consume, or read. Consumers are assigned to partitions
  independently if they are not explicitly declared by the developer.
