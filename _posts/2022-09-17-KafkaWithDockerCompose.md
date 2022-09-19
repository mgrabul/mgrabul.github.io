---
title: Simple Kafka docker-compose explained
date: 2022-06-15
categories: [Kafka]
tags: [TAG]     # TAG names should always be lowercase
---
 
## Prerequisites
* Installed docker and docker-compose
* Optional, (Offset Explorer (formerly Kafka Tool))[https://www.kafkatool.com/download.html] UI tool to view the content of the kafka system that includes the nodes, topics, and consumers
 
## Simple Kafka docker-compose explained
The best way to start learning and using Kafka is with docker, docker-compose. The simplest configuration of Kafka will be to have one zookeeper node and one broker node in a docker-compose file but most of the examples in the books and tutorials teach Kafka in an environment with one Zookeeper node and three broker nodes.
 
The simplest docker-compose file will look like the following:
```yaml
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - 22181:2181

  broker1:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - 29092:29092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker1:9092,EXTERNAL_LISTENER://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL_LISTENER

  broker2:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - 29093:29093
    environment:
      KAFKA_BROKER_ID: 2
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker2:9092,EXTERNAL_LISTENER://localhost:29093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL_LISTENER
  broker3:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - 29094:29094
    environment:
      KAFKA_BROKER_ID: 3
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker3:9092,EXTERNAL_LISTENER://localhost:29094
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL_LISTENER
```
As it can be noticed, all the brokers need different names, ports, KAFKA_BROKER_ID and appropriate KAFKA_ADVERTISED_LISTENERS.
 
## Optional using Offset explorer
To visually see and explore the Kafka cluster [Offset Explorer](https://www.kafkatool.com/download.html) can be used. Download from [here](https://www.kafkatool.com/download.html) depending on your platform. After installing set the following values in order to connect to the cluster:
* Properties -> Name of cluster: Choose a name for your cluster
* Properties -> Zookeeper Host: localhost
* Properties -> Zookeeper Port: 22181
* Advanced -> Bootstrap Server: localhost:29092 (the external address of a broker(s))
## Reference:
* https://rmoff.net/2018/08/02/kafka-listeners-explained/
* https://www.baeldung.com/ops/kafka-docker-setup
* Kafka in Action by Dylan Scott, Viktor Gamov, Dave Klein
