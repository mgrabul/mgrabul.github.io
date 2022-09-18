---
title: Setting up WireGardVPN at home
date: 2022-06-15
categories: [VPN, Wireguard]
tags: [TAG]     # TAG names should always be lowercase
---

## Prerequisites
* Installed docker and docker compose
* Optional, (Offset Explorer (formerly Kafka Tool))[https://www.kafkatool.com/download.html] UI tool to view the content of the kafka system that includes the nodes, topics and consumers

## Simple kafka docker-compose explained
Best way to start learning and using using kafka is with docker, docker-compose. The simplest configuration of kafka will be to have one zookeeper node and one broker node in a docker-compose file but most of the examples of in the books and tutorials teach kafka in an environment with one Zookeeper node and three broker nodes.

The simples docker-compose file will look like the following:
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
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181 # zookeeper defined internal Ip address
      KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker:9092,EXTERNAL_LISTENER://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL_LISTENER
```
The docker-compose file contains two services, zookeeper and broker. 
### Zookeeper
Lets first look at the relevant configuration for zookeeper. The port that other services can use to communicate with zookeeper is defined `ZOOKEEPER_CLIENT_PORT: 2181`. In our case this the port is 2181. **This port is available to be used by the services in the same docker network. This are all the services defined in this docker-compose file. Services outside of this docker-compose file will not be able to use 2181 to communicate with the Zookeeper **. To expose Zookeeper to the outside the docker port mapping is used 
```yaml
  ports:
      - 22181:2181
```
Now external services can communicate with zookeeper from port 22181 and services from this docker-compose file can use 2181.

### Broker
Every broker with in the kafka cluster needs to have a unique id defined by `KAFKA_BROKER_ID` and in this case `KAFKA_BROKER_ID: 1` the id is 1. Every broker node needs to know the address of Zookeeper, the zookeeper localtion is defined in `KAFKA_ZOOKEEPER_CONNECT` and in this case `KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181`, `zookeeper:2181` it is the name of the zookeeper service followed by the internal port exposed by the zookeeper service. The brokers location/addresses is configured by `KAFKA_ADVERTISED_LISTENERS` in this case `KAFKA_ADVERTISED_LISTENERS: INTERNAL_LISTENER://broker:9092,EXTERNAL_LISTENER://localhost:29092` the formant of the addresses(listeners) is <NameOfListener>://<NameOfService>:<PORT>. Here I have defined tow listeners and internal and external listener. External is used by services or consumers and producers that are not in this docker network. Internal will be used by consumers and other services that are part of this docker-compose network. `KAFKA_LISTENER_SECURITY_PROTOCOL_MAP` defines the type of the listener, `KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL_LISTENER:PLAINTEXT,EXTERNAL_LISTENER:PLAINTEXT` here bought listeners `KAFKA_LISTENER_SECURITY_PROTOCOL_MA`P` and `INTERNAL_LISTENER` are from type `PLAINTEXT`. The `KAFKA_INTER_BROKER_LISTENER_NAME` is used to insure that internal only `INTERNAL_LISTENER` will be an internal listener, used by internal consumers.

### Broker