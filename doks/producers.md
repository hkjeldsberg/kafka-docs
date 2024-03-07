# Working with Producers

## Building a Producer in Java

Data can be published to data though Kafka's Java **Producer API**. A producer is created using the `KafkaProducer`
class:

```java
Producer<String, String> producer = new KafkaProducer<>(props);

// Send one record/log
int i = 0;
ProducerRecord record = new ProducerRecord<>("test_count", partition, "count",Integer.toString(i));
producer.send(record)
producer.close()
```

