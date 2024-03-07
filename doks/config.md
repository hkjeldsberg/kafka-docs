# Kafka configuration

## CLI configuration

Modifying broker configuration can be performed by editing `server.properties` or by using `kafka-configs`. Given a
current configuration for a broker (1):

```console
kafka-configs --bootstrap-server localhost:9092 --entity-type brokers --entity-name 1 --describe
```

We can modify this configuration by passing the `--alter` command:

```console
kafka-configs --bootstrap-server localhost:9092 --entity-type brokers --entity-name 1 --alter --addconfig log.cleaner.threads=2
```

Configurations can also be specified when creating a new topic with `kafka-topics`:

```console
kafka-topics .... --config max.message.bytes=64000
```

Similarly, these topics can be altered by using `--entity-type topics` with `kafka-configs`.

```console
kafka-configs --zookeeper localhost:2181 --entity-type topics --entity-name configured-topic --alter
--add-config max.message.bytes=65000
```

Furthermore, a **broker-wide** default topic configuration may be applied using `kafka-configs:

```console
kafka-configs --bootstrap-server localhost:9092 --entity-type brokers --entity-name 1 --alter --addconfig message.max.bytes=66000
```

## Programmatic configuration

Clients can be confuigured programmatically in Java using a `Properties` object:

```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
Producer<String, String> producer = new KafkaProducer<>(props);
```

## Things to consider when configuring Topics

When designing topics, consider the following:

- How many brokers are available? (limits number of replicas)
- What is the need for fault tolerance (through replication factor)
- How many consumers per consumer group (determines number of partitions)
- How much memory is available (brokers have default 1 MB per partition)