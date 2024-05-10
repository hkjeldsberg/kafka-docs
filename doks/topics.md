# Topics Fundamentals

A **topic** is a collection of related messages or events – a log or sequence of events. In theory, you can have an
unlimited number of topics. Within a topic, we find one or several **partitions** corresponding to a log, which are
allocated to different brokers in the cluster. You can think of partitions as a log; strictly ordered with each message
receiving an incremental ID called **offset**. Within a partitions, we have individual **segments**, existing on the
disk. Kafka performs distributions of the topics to brokers, but the user needs to set resource limitations and
requests.

## Topic Design

The two main considerations when creating topics is **partitions** and **replication factor**.
This is controlled by:

- Number of brokers, which limits number of replicas
- High replication factor implies greater fault tolerance

Regarding consumers: how many consumers are needed per consumer group?
At least as any partitions as number of consumers on a single group is required.

Regarding memory: how much memory is available on each broker?
Required memory can be adjusted with configuration `replica.fetch.max.bytes`, which defaults to 1 MB for each partitions
on a broker.

## Topic Tools

Topics are created in the following way with a given `--replication-factor` and number of `--partitions`:

```console
kafka-topics --bootstrap-server kafka:9092 --create  --topic <topic-name>  --partitions $PART  --replication-factor $REP
```

To (1) avoid overwriting an existing topic and (2) create a topic that does not exsist, pass the `--if-not-exists` flag.

Number of partitions can be altered using the `--alter` flag:

```console
kafka-topics --bootstrap-server kafka:9092 --alter  --topic <topic-name>  --partitions $NEW_NUMBER_OF_PARTITIONS
```

where `<topic-name>` is an existing topic. You can only **increase** the number of partitions.

Other topic commands include:

- `--topics-with-overrides`
- `--under-replicated-partitions`
- `--unavailable-partitions`

## Topic Configuration

To get configuration for a particular entity type (here topics) may be listed by describing the `--entity-type`
to `kafka-configs`:

```console
kafka-configs --bootstrap-server kafka:9092 --describe --entity-type topics
```

and add configuration by using the `--add-config KEY=VALUE` flag (also need to specify `--entity-name`).

Another approach to configure topics is through *consumer groups* accessible through `kafka-consumer-groups.sh`.
Deleting a group with `--delete --group <group-name>` can only be performed for consumer groups with no
members (`-member`). To reset the offset for an existing consumer group, use `--to-latest`. When listing topics, the
topic `__consumer_offset` topic will be listed with information of the offset. This is created when *consumer groups*
are enabled.

## Message behaviour

By default, messages are read one per line. This can be controlled by a class extending the `kafka.common.MessageReader`
class. Given an extended class `MyMessageReader`, this can be supplemented to the Kafka console through the
argument `--line-reader org.example.MyMessageReader`.