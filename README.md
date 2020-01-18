# kafka-essentials
Apache Kafka 2.0 Ecosystem, Core Concepts, Real World Java Producers/Consumers &amp; Big Data Architecture

## Getting started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See the deployment section for notes on how to deploy the project on a live system.

## Prerequisites

- [Java 8 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [Kafka 2.0.0](https://archive.apache.org/dist/kafka/2.0.0/kafka_2.12-2.0.0.tgz). Alternatively, make use of homebrew `brew install kafka` 
Take note of the location where kafka is installed

- Java IDE - e.g IntelliJ
- Create elastic search cluster (Can use [bonsai](https://bonsai.io/)) 

## Installing

Step by step instructions on how to get a working version of the project on your local machine, such as

- Clone repository
- Run `mvn clean install` - see if there are any errors
- Start Zookeeper server `zookeeper-server-start.sh config/zookeeper.properties` or for homebrew use `zkServer start`
- Start Kafka service `kafka-server-start.sh config/server.properties`
- Start consumer `kafka-console-consumer.sh --bootstrap-server <127.0.0.1:9092> --topic <my_topic> --group <my_consumer_gp>`

### Helpful commands

Create topic ```kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic TOPIC_NAME --create --partitions 3 --replication-factor 1```

List topics ```kafka-topics.sh --zookeeper 127.0.0.1:2181 --list```

More info on a topic ```kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic TOPIC_NAME --describe```

Delete topic (Don't do this in windows) ```kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic TOPIC_NAME --delete```

### Run code scripts

The project has two modules: 
- kafka-basics
- kafka-producer-twitter

#### kafka-basics
Using Code (example of running producer)
- Open the codebase (intellij) and navigate to ``kafka-basics/src/main/java/kafka.tutorial1``
- Run the ``ProducerDemo`` file by right-clicking on the file and choose `Run`

- ProducerDemo - produce a message 
- ProducerDemoWithCallback - produce messages, capture status of delivery for each message 
- ProducerDemoWithKeys - produce messages associated with a key to go to the same partition

- ConsumerDemo - consume a message
- ConsumerDemoGroups - consume messages for a group
- ConsumerDemoWithThread - consume messages using multiple threads (each consumer has it's own thread)
- ConsumerDemoAssignSeek -  consumer. This doesn't use a groupid or subscribe to a topic. This is used to replay data or fetch a specific message 

#### kafka-producer-twitter
- TwitterProducer - Produce tweets to elastic search (setup in bonsai required). Consumer processing is idempotent

### Producer

Using kafka commands

Produce to topic ```kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic TOPIC_NAME```

Produce to topic with acks=all ```kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic TOPIC_NAME --producer-property acks=all```

Producer with Keys: 
```
kafka-console-producer --broker-list 127.0.0.1:9092 --topic TOPIC_NAME --property parse.key=true --property key.separator=,
> key,value
> another key,another value
```

A little more on acks:
- `acks=0` is performant, useful on metrics & logging (cases where we can manage losing a msg if needs be)
- `acks=1` is the default, the leader acknowledges every request
- `acks=all` the replicas  acknowledges, this adds latency & guarantees. (set min.insync.replicas)


### Consumer

Using kafka commands

Consume from beginning: ```kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic TOPIC_NAME --from-beginning```

Create a consumer group for consumption: ```kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic TOPIC_NAME --group GROUP_NAME```

List consumer groups: ```kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list```

More info on consumer group (view: partitions, lag, offsets): ```kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group GROUP_NAME```
Useful command to answer questions such as: What is my lag? Which service is currently consuming from this group?

Reset offset by using: `--reset-offsets`

Consumer with Keys: 
```
kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic TOPIC_NAME --from-beginning --property print.key=true --property key.separator=,
```

### Zookeeper
Validate Zookeeper running / health 
```
echo stat | nc <zookeeper ip> 2181
echo mntr | nc <zookeeper ip> 2181
echo isro  | nc <zookeeper ip> 2181
```

### UI
Alternative to using the CLI to manage kafka, there is a UI:
[Kafka Manager](https://github.com/yahoo/kafka-manager)

### Optimisation 
Here are some properties to consider changing for optimising kafka
- COMPRESSION_TYPE_CONFIG
- LINGER_MS_CONFIG
- BATCH_SIZE_CONFIG

# Source
[Apache Kafka 2.0](https://www.udemy.com/course/apache-kafka/)
