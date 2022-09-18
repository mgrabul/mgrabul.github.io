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
 
 broker:
   image: confluentinc/cp-kafka:latest
   depends_on:
     - zookeeper
   ports:
     - 29092:29092 # externally available port
   environment:
     KAFKA_BROKER_ID: 1 # Id of the broker, different for every broker service
     KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181 # zookeeper defined internal IP address
     KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker:9092,EXTERNAL_LISTENER://localhost:29092
     KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT
     KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL_LISTENER
```
The docker-compose file contains two services, Zookeeper, and a broker.
### Zookeeper
Lets first look at the relevant configuration for Zookeeper. The port that other services can use to communicate with Zookeeper is defined as `ZOOKEEPER_CLIENT_PORT: 2181`. In our case, the port is 2181. **This port is available to be used by the services in the same docker network. These are all the services defined in this docker-compose file. Services outside of this docker-compose file will not be able to use 2181 to communicate with the Zookeeper **. To expose Zookeeper to the outside the docker port mapping is used
```yaml
 ports:
     - 22181:2181
```
Now external services can communicate with Zookeeper from port 22181 and services from this docker-compose file can use 2181.
 
### Broker
Every broker within the Kafka cluster needs to have a unique id defined by `KAFKA_BROKER_ID` and in this case `KAFKA_BROKER_ID: 1` the id is 1. Every broker node needs to know the address of Zookeeper, the zookeeper location is defined in `KAFKA_ZOOKEEPER_CONNECT` and in this case `KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181`, `zookeeper:2181` it is the name of the zookeeper service followed by the internal port exposed by the zookeeper service. The broker’s location/address is configured by `KAFKA_ADVERTISED_LISTENERS`. In this case `KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker:9092,EXTERNAL_LISTENER://localhost:29092`. The format of the addresses(listeners) is <NameOfListener>://<NameOfService>:<PORT>. Here I have defined two listeners, one internal and one external listener. External listeners is used by services or consumers and producers that are not in this docker network. Internal will be used by consumers and other services that are part of this docker-compose network. `KAFKA_LISTENER_SECURITY_PROTOCOL_MAP` defines the type of the listener, `KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT, EXTERNAL_LISTENER:PLAINTEXT` here bought listeners are `PLAINTEXT`. The `KAFKA_INTER_BROKER_LISTENER_NAME` is used to ensure that only the listener `INTERNAL_LISTENER` is used by internal consumers.
 
## Kafka simple docker-compose file
For Kafka learning purposes a docker-compose file will usually contain one zookeeper instance and three Brokers. Here is an example of that kind of setup:
 
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
