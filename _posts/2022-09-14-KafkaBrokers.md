# Kafka Brokers

# Prerequisites
* use [kafka visual tool](https://kafkatool.com/download.html) as a UI to explore the the kafka cluster 
Terms: 
* Racks awareness, partition replicas exist on separate racks and the system is aware of this
* Cluster ????
* Zookeeper cluster

Kafka is composed od two different clusters, Brokers cluster and ZooKeeper cluster.
Note: There is an idea to replace ZooKeeper with own managed quorum.
Before starting the broker cluster the Zookiper cluster needs to be running.

** WHAT IS KAFKA, check this is it the brokers cluster + the Zookeper? If so update if necessary the previous paragraph **

Zookipper cluster is used for orcestration/managment of the brokers in the Broker cluster. This implies that the brokers require running zookeeper already running. Here is a simple docker-compose file containing one Zookiper node and three brokers. The brokers have the task to store the data produced by the producer clients and provide the data for consuming to the consumer clients. When a consumer or a producer client wants to either store/produce data or consume data, at start it will ask any of the cluster node for the address and port of the broker that will handle the data. On the second step the consumer aether reads or writes the data to the broker.  

## Important files and commands
* kafka auto competition https://github.com/Kafka-In-Action-Book/kafka_tools_completion
* kafka-zsh-completions https://github.com/Dabz/kafka-zsh-completions

## Reference:
* https://www.baeldung.com/ops/kafka-docker-setup
* https://github.com/Kafka-In-Action-Book/Kafka-In-Action-Source-Code/blob/master/docker-compose.yaml
* https://rmoff.net/2018/08/02/kafka-listeners-explained/
* Kafka in Action by Dylan Scott, Viktor Gamov, Dave Klein

## External Important notes for other blogs
* 
```bash  
    jps # is a java command for listing java processes -> add it to important commands name is: java + ps where ps is a command to list all processes 
``` 
* 
```bash
    lsof # command for finding processes 
``` 