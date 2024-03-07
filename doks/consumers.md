# Working with Consumers
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
