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