# Working with Producers


Written in any language or can be initiated throught the command line. With no key for partitioning stratergy the
producer will use a *round-robing* strategy. Alternative the default strategy is `hash(key) % number_of_partitions`.
Messages of the same key land in order.

The standard producer (in `Java`) contains of several fundamental blocks. This includes the **configuration** with
information about the cluster. Then you create the producer using the `KafkaProducer` passing the configuration. Then a
part which produces data and sends it to the `producer`, using the class `ProducerRecord` with the topic of interest.
See figure below.

```{figure} figures/producer-java.png
---
name: producer
---
A representative producer in Java.
```

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

## Producer design

What happends when you call `Send()`? First, it goes through **serialization**, turning it into the bytes. This is
passed to the **partitioner** checking for a key, alternatively hashing it and deciding which patition it will be
written to.

What happens in the broker? If `NONE` (Acks 0) sends it, we have no info what happened in the broker. If the `LEADER` (
Acks 1) sends the data, and waits for acknowlegement. Finally, `ALL` (Acks -1) means the leader and all its replicas
have sent data. Safest but produces more latency.

Three processes can happen: "At most once" (may lose data), "At least once" (everythig goes through, may be doubled),
and "Exactly once" (one to one).

- **At least once** (Default): Does not result in data loss, processes one or more times, and may result in duplicates
- **At most once**: Can result in data loss, processes once (or never), but has *no* duplicates. Useful for checking traffic.
- **Exactly once**: Does not result in data loss, processes exactly once, and does *not* produce duplicates
    - This semantic prevents duplication, Handles failures gracefully, and is a strong transactional guarantees for
      Kafka.
