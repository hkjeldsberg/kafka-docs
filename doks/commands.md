(cmd-imp)=

# Kafka Basics

Data is described by logs or **topics**. An alternative way to store data as opposed to databased.
**Events** are stored in the topics. Currently, the trend is to create smaller applications that can communicate through
the Kafka topic. This enables *real-time* analysis of data stored in topics, instaed of batch processing. Writing data
from a database into a topic can be done with **Kafka connect**. This framework is also used to input data from topics
to other services.
**Kafka streams API** handles framework and infrastructure to connect/join topics. For database queries Kafka
includes **kSQL**, which outputs into a topic.
**Kafka cloud** enables cloud services with Kafka.

## Tough concepts of Kafka

Main misconception: "Kafka is a queue". Instead, **Kafka is a log**. Second: event streams are **immutable**. If
databases are brief "snapshots" in time, Kafka is a "live broadcast". Leads to Stream/Table duality; one can be derived
by the other. Third: materialized view of database query – using **kSQLdb**, which updates the data based on the stream.

## Why Kafka and log system?

Fist, consider the existing techonology:
Pros:

- Suitable for heavy, complex data transformations and aggregations.
- Optimized for high-volume, periodic updates and historical data analysis.

Cons:

- Latency issues; not suited for real-time data processing.
- Complex and potentially costly ETL (Extract, Transform, Load) processes.
- Scalability can be challenging, requiring significant resources.

In contrast, because there is a growing interest in real-time processing of data, we propose the use of **logs**. Pros:

- Enables real-time data processing (ETL) and immediate data availability to multi-subscriber data feeds.
- Simplifies data integration across distributed systems.
- Enhances system resilience and data recovery through append-only nature.
- Logs are at the heart of *stream processing*; infrastructure for continuous data processing.
- Logs act as a buffer allowing processes to be restarted or fail withoyt slowing down other parts of the processing
  graph.

## Kafka Fundamentals

A **producer** – a service that writes events to the Kafka cluster. A **Kafka cluster** consists of one or several **
brokers**, with each its local storage. Produces write into these storages. Brokers may be machines, VMs, servers,
containers, or similar. To read data from the cluster, we use a **consumer**. A lot of the work happens on the
consume-side of the framework. The consumed data may then be further processed and presented in reports, dashboards or
visualizations, ect. Producers and consumers are **de-coupled**  – hence slow consumers will not affect producers and
vice versa. Access control, cluster management, and failure detection & recovery is controlled by a leader broker node (
previously *Zookeeper*). A **topic** is a collection of related messages or events – a log or sequence of events. In
theory, you can have an unlimited number of topics. Within a topic, we find one or several **partitions** corresponding
to a log, which are allocated to different brokers in the cluster. You can think of partitions as a log; strictly
ordered. Within a partitions, we have individual **segments**, existing on the disk. Kafka performs distributions of the
topics to brokers, but the user needs to set resource limitations and requests.

```{figure} figures/log.png
---
name: log
---
A representation of a log in Kafka.
```

A log consists of several **events** which contitutes a **stream**. Each Kafka message consists of a **key** and
**value**, with an optional **header** and a **timestamp** (creation/ingestion time).

## Brokers

Basic function: manage partitions locally. Takes input from producers and places them in partitions. Recieves requests
from consumers and let them read from partitions. Each partition has replicas (controller by replication factor), such
that partitions are replicated on separate brokers working as a backup.

## Producers

Written in any language or can be initiated throught the command line. With no key for partitioning stratergy the
producer will use a *round-robing* strategy. Alternative the default strategy is `hash(key) % number_of_partitions`.
Messages of the same key land in order.

## Consumers

A consumer pull messages from 1, ..., $n$ topics. The consumer offset is also stored in memory, and is stored in a
special topic: *consumer offset* which keeps track of the consumers "position". Consumers can also belong to a consumer
group.

## Creating topics, producers and consumers (Command-line)

Connect to a Kafka server and create a topic using:

```console
kafka-topics --bootstrap-server kafka:9092 --create  --topic <topic-name>  --partitions $PART  --replication-factor $REP
```

Then view active Kafka topics using the `--list` command: `

```console
kafka-topics --bootstrap-server kafka:9092 --list
```

Then to produce data use the `kafka-console-producer` command to add data (optionally with a key):

```console
kafka-console-producer --broker-list kafka:9092  --topic sample-topic --property parse.key=true  --property key.separator=,
```

And view the produced data using the `kafka-console-consumer` command:

```console
$ kafka-console-consumer --bootstrap-server kafka:9092 --topic sample-topic --from-beginning --property print.key=true
```

Similarly, to view consumer groups, we can use `kafka-consumer-groups`:

```console
kafka-consumer-groups --bootstrap-server kafka:9092 --group vp-consumer --describe
```

or to continuously watch the process, use `watch`:

```console
watch kafka-consumer-groups --bootstrap-server kafka:9092 --group vp-consumer --describe
```

## How Kafka works

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

## Leadershit and Replication within a Partition

When multiple partitions of the same topic are created, one act as the **leader** and the remaining as **followers**.
The leader is telling producers and consumers that they should be written to, and similarly the consuming comes from
this partition. When a broker crashes and contains a leader, a **new leader** is elected right away, with no loss of
data.

## Data retention

By default, data is stored for a time of `1 week`, but his can be edited either by per topic, or globally. How often the
data is stored depends on the business logic, and GDRP and similar compliance perspectives. When data is expired, it is
purged per segment.

## Producer design

What happends when you call `Send()`? First, it goes through **serialization**, turning it into the bytes. This is
passed to the **partitioner** checking for a key, alternatively hashing it and deciding which patition it will be
written to.

What heppends in the broker? If `NONE` (Acks 0) sends it, we have no info what happened in the broker. If
the `LEADER` ((Acks 1) sends the data, and waits for acknowlegement. Finally, `ALL` (Acks -1) means the leader and all
its replicas have sent data. Safest but produces more latency.

Three processes can happen: "At most once" (may lose data), "At least once" (everythig goes through, may be doubled),
and "Exactly once" (one to one).

## Exactly once semantics

Prevents duplication. Handles failures gracefully and is a strong transactional guarantees for Kafka.

## Consumer groups

Consider two producers, producing two unique topics. Then two consumers can consume both topics. How is this assigned
when the consumers have the same **group ID**? The consumers are doing its best at allocating the partitions. What if we
scale the consumer? Then the partition reassigns partitions automaticalyl. If something failes, a new rebalance happens
automatically. However, the consumer API does *not* consider **state**.

## Compacted topics

The time values are ordered, and the values vary. However, the **key** may be varying only between key1 to key4 (
example), e.g. 4 unique keys. If previous states are not important, use a **compacted topic** which removes "old"
entries in the partition.

## Security

Kafka will encrypt using SSL or similar all types of transits (producer to broker, broker to consumer, ect.). Kafka also
supports authentication and authorization, but using security is *optional*. However,

## Integrating Kafka into you Environment

Kafka can be integrated with any system; through **Kafka Connect API** – a data integration framework to get data in and
out of Kafka. Connect runs on a separate server, between the source and the Kafka cluster. Similarly, betwene the Kafka
cluster and the sink. Lots of these connectors already exists between existing frameworks and Kafka Connect. Connect is
a distrituted system with $n$ workers running one or more **tasks**. Each task runs with a connector, which can again be
partitioned into multiple pieces. Connectors can thus be split between multiple workers.

## Confluent REST Proxy

Used to talk to non-native Kafka Apps and outside the Firewall – using a RESTful interface.

## Schema evolution

This can be solved using a Schema Registry; a type descrition will translate into a expected field for each Kafka
topics. A schema registry works outside the Kafka Cluster, and are kepy in an internal topic in the Kafka cluster,
communicating with the Producer and Consumer. Consumer can ask for schema though a **schema ID**, to recognize the data
format.

Avro: a container file which consists of a *metadata* (schema) part and a *data* part, where the schema syntax is
similar to `json`.

## Confluent ksqlDB

Streaming SQL enging for Kafka using SQL-like semantics. Example use cases include streaming ETL, anomaly detection and
event monitoring. The system can be accessed via CLI, REST or headless using any programming language.

## Kafka Streams

Kafka Streams is a Java API for doing stream processing on data in Apache Kafka. This combines the streams for Java with
the Kafka, particularly usefull for microservices and continous queries and transformations. 

