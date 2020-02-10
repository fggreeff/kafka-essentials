# kafka-essentials
Apache Kafka 2.0 Ecosystem, Core Concepts, Real World Java Producers/Consumers &amp; Big Data Architecture

## Getting started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See the deployment section for notes on how to deploy the project on a live system.
For a single-node development environment setup, have a look at the confluent setup section.

## Prerequisites

- [Java 8 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [Kafka 2.0.0](https://archive.apache.org/dist/kafka/2.0.0/kafka_2.12-2.0.0.tgz). Alternatively, make use of homebrew `brew install kafka` 
Take note of the location where kafka is installed

- Java IDE - e.g IntelliJ
- Create elastic search cluster (Can use [bonsai](https://bonsai.io/)). Generate auth keys
- Twitter keys for API usage - [Generate new keys](https://developer.twitter.com/en/apps/)

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
- TwitterProducer - Produce tweets to a topic

#### kafka-consumer-elasticsearch
- TwitterProducer - Consume tweets from a topic and put on elastic search (setup in bonsai required). Consumer processing is idempotent, optimised for batching whilst committing offsets manually 

#### kafka-connect
- Kafka-connect - Making use of a Twitter connector to produce a stream of tweets onto a topic

`cd kafka-essentials/kafka-connect`

`connect-standalone connect-standalone.properties twitter.properties`

#### kafka-streams-filter-tweets
- StreamsFilterTweets - Making use of Kafka streams to filter twitter messages

Run `TwitterProducer` to produce tweets

Run `StreamsFilterTweets` to filter tweets with over 1000 followers and put on topic 'important_tweets'

Run Consumer for topic 'important_tweets' `kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic important_tweets --from-beginning`

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
```kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --group GROUP_NAME --topic TOPIC_NAME --reset-offsets --execute --to-earliest```

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

### Security

- Encryption: Use SSL encryption port 9093 for brokers
- Authenticate: SSL auth or SASL auth (plain, kerberos, Scram)
- ACL (Access control list): Auth to read / write topics

### Advanced topic configuration

View configuration on a topics: 
```kafka-configs --zookeeper 127.0.0.1:2181 --entity-type TOPIC_NAME --describe```

Add configuration on a topics: 
```kafka-configs --zookeeper 127.0.0.1:2181 --entity-type TOPIC_NAME --add-config CONFIG_TO_ADD --alter```

Delete configuration on a topics: 
```kafka-configs --zookeeper 127.0.0.1:2181 --entity-type TOPIC_NAME --delete-config CONFIG_TO_ADD --alter```

Min InSync replicas (can be set at broker or topic level)
- Topic: Add `min.insync.replicas` configuration on a topics: ```kafka-configs --zookeeper 127.0.0.1:2181 --entity-type topics --entity-name TOPIC_NAME --add-config min.insync.replicas=2 --alter```
- Broker: cd `kafka` folder, open `vim config/server.properties` and set `min.insync.replicas=2`

Partitions & segments
- log.segments.bytes: How fast will I have new segments based on throughput? 
- log.segments.ms: How often should log compaction happen? 

Logs
- log.cleanup.policy: How often should logs be cleaned up. Take note of CPU/memory usage
- log.retention.hours: Number of hours to keep data for (Default 1 week)
- log.retention.bytes: Max size for each partition (Default -1 (Infinite))

### Confluent setup (Under 5min)
A single-node development environment for linux or MacOS. 
- Install [Confluent CLI](https://docs.confluent.io/current/cli/cli-autocompletion.html#macos) 
- Start ZK, Kafka, Kstreams, KSQL, UI: `./confluent local start`. To start ZK & kafka only, run `./confluent local start kafka` 
- Stop all services: `./confluent local stop` 
- Delete / Destroy: `./confluent local destroy`

### Dockerised ZK / Kafka 
[Start Kafka using Docker](https://github.com/simplesteph/kafka-stack-docker-compose)


# Source
[Apache Kafka 2.0](https://www.udemy.com/course/apache-kafka/)

[kafka-beginners-course](https://github.com/simplesteph/kafka-beginners-course)

[Kafka connectors](https://www.confluent.io/product/connectors-repository/)

[Twitter connector](https://github.com/jcustenborder/kafka-connect-twitter)

### Advanced topic configuration
[List of config](https://kafka.apache.org/documentation/#configuration)

### Extra
[Which kafka to use?](https://medium.com/@stephane.maarek/the-kafka-api-battle-producer-vs-consumer-vs-kafka-connect-vs-kafka-streams-vs-ksql-ef584274c1e)
