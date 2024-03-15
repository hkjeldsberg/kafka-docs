# Working with Consumers

A consumer pull messages from 1, ..., $n$ topics. The consumer offset is also stored in memory, and is stored in a
special topic: *consumer offset* which keeps track of the consumers "position". Consumers can also belong to a consumer
group.

For the consumder (here in `C#`), start the consumer file with some configuration. Then initialize some event-handlers,
which handles messages and potential errors. The consumer is initialized using the `Consumer` class, with a
function `Subscribe` where the `topic` of interest needs to be specified (multiple topics can be subscribed to).
A `while (true)`-loop is then initiated to `Poll` (pull, consume) the date from the Kafka cluster. See figure below.

```{figure} figures/consumer-c.png
---
name: consumer
---
A representative consumer in C#.
```

## Building a Consumer in Java

A consumer can be built though the Java **Consumer API**. The `KafkaConsumer` class will handle interaction between the
Java code and Kafka:

```java
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

// Subscribe to topic(s)
consumer.subscribe(Arrays.asList("test_topic1", "test_topic2"));

// Fetch records:
ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
// Perform some operations on records..

consumer.commitSync();
```

## Consumer groups

Consider two producers, producing two unique topics. Then two consumers can consume both topics. How is this assigned
when the consumers have the same **group ID**? The consumers are doing its best at allocating the partitions. What if we
scale the consumer? Then the partition reassigns partitions automaticalyl. If something failes, a new rebalance happens
automatically. However, the consumer API does *not* consider **state**.
