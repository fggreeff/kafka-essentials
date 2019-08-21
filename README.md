# kafka-essentials
Apache Kafka 2.0 Ecosystem, Core Concepts, Real World Java Producers/Consumers &amp; Big Data Architecture

## Getting started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See the deployment section for notes on how to deploy the project on a live system.

## Prerequisites

- Java 8 JDK -  http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
- Kafka 2.0.0 - https://archive.apache.org/dist/kafka/2.0.0/kafka_2.12-2.0.0.tgz
- Java IDE - e.g IntelliJ

## Installing

Step by step instructions on how to get a working version of the project on your local machine, such as

- Clone repository
- Start Zookeeper server `zookeeper-server-start.sh config/zookeeper.properties`
- Start Kafka service `kafka-server-start.sh config/server.properties`
- Start consumer `kafka-console-consumer.sh --bootstrap-server <127.0.0.1:9092> --topic <my_topic> --group <my_consumer_gp>`

# Source

[Apache Kafka 2.0](https://www.udemy.com/course/apache-kafka/)
