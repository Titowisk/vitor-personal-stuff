*This document is mostly based on the Confluent Platform solution for Apache Kafka, not the open source kafka.
# Kafka Concepts

## Topics
A topic is a log of events. Logs are also fundamentally durable things

- they are append only: When you write a new message into a log, it always goes on the end. 
- they can only be read by seeking an arbitrary offset in the log, then by scanning sequential log entries. 
- events in the log are immutable

## Partition
Each partition is a duplicate? of the events of a topic and can be stored in different nodes of a cluster

- if the key of the key-value pair is null, new messages are stored round robin among the partitions
- messages with the same key are always assigned to the same partition. So this guarantees order among messages with the same key

## Replication
- copies of data for fault tolerance
- one lead partition and n-1 followers
- in general, writes and reads happens to the leader
- an invisible process to developers

## Producers
- there can be multiple producers for the same topic: it depends on partitions

## Consumers
- consumers can ingest from the same topic
- consuming is non-destructive
- consumers are horizontally scalable, but the number of consumer can scale up to the number of partitions

# Kafka Components
- confluentinc/cp-kafka [Broker]
- confluentinc/cp-schema-registry [schema-registry]
- confluentinc/cp-enterprise-control-center [control-center]
- confluentinc/cp-ksqldb-server [ksqldb-server]
    - confluentinc/cp-ksqldb-cli [ksqldb-cli]
- confluentinc/cp-kafka-rest [rest-proxy]

## Broker
- A computer, instance or container running the kafka process
- manages partitions
- handle write and read requests from partitions
- manage replication of partitions

## Schema Registry
> Schema Registry provides a centralized repository for managing and validating schemas for topic message data, and for serialization and deserialization of the data over the network

- This component can prevent producers to produce incompatible messages and consumers to consume incompatible messages.
- producers and consumers communicate with the schema registry using api

### Standard Formats
- Avro
- Json
- Protocol Bufer

## Kafka Connect
> On the one hand, Kafka Connect is an ecosystem of pluggable connectors, and on the other, a client application. As a client application, Connect is a server process that runs on hardware independent of the Kafka brokers themselves. It is scalable and fault-tolerant, meaning you can run not just one single Connect worker but a cluster of Connect workers that share the load of moving data in and out of Kafka from and to external systems.

- data integration system and ecosystem

### Kafka Connect Worker
A Connect worker runs one or more connectors.
- A source connector reads data from an external system and produces it to a Kafka topic
- A sink connector subscribes to one or more Kafka topics and writes the messages it reads to an external system

## Kafka Streams
Kafka Streams is a Java API that gives you easy access to all of the computational primitives of stream processing: filtering, grouping, aggregating, joining, and more,

And that means saving state somewhere.

- Scalable, fault-tolerant state management
- Scalable computation based on Consumer Groups
- Integrates within your services as a library
- Runs in the context of your application

# References
- https://developer.confluent.io/courses/apache-kafka/events/ 

# Notes

## Scalability of Partitions
In order to scale partitions the best practice is to use hash algorithms. 
For example, if you have 100_000 of entities, you cant use entityId as the kafka message key.
You have to use a hash algorithm that will group this entitites in `n` partitions

# Random comments

## What is this
Why are there some much moving parts in a supposebly symple quick start on Confluent Platform ?

The quickstart: https://docs.confluent.io/platform/current/get-started/platform-quickstart.html 
docker-compose file from the quickstart https://github.com/confluentinc/cp-all-in-one/blob/7.9.0-post/cp-all-in-one-kraft/docker-compose.yml

## Dont remember
https://quix.io/blog/kafka-auto-offset-reset-use-cases-and-pitfalls
https://docs.confluent.io/platform/current/clients/consumer.html wtf is this


# Consumer Groups and record offset
- https://codingharbour.com/apache-kafka/what-is-a-consumer-group-in-kafka/

- consumer groups are groups of consumers
- multiple partitions can be consumed by multiple members of one consumer group
    - each partition is consumed by only one member of a consumer group
- a topic can have multiple partitions
- different consumer groups can consume the same topic

- a partitions is a list of records
- each record have an offset id
- consumer groups commit offset ids to indicate records consumed
    - they are stored in a special Kafka topic called `__consumer_offsets`


- as long as the group_id of a consumer groups remains the same, consumers of that group can pick up
messages from the partition from the committed offset

