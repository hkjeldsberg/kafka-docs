# Metrics and Monitoring

Kafka metrics are accessed using `JMX`, accessable by passing a `JMX`-option via the `KAFKA_JMX_OPTS` env variable. This
can report to logging systems such as Grafana or Splunk.

```console
KAFKA_JMX_OPTS="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false -
Djava.rmi.server.hostname=localhost"
```

Kafka also uses Yammer Metrics, for metrics reporting in the server.

## Common Broker Metrics

Common broker metrics include:

- **ACTIVE CONTROLLER COUNT**: Is the broker the controller?
- **REQUEST HANDLER IDLE RATIO**: How much load is the broker under?
- **ALL TOPICS BYTES IN**: Do I have enough brokers?
- **ALL TOPICS BYTES OUT**: High consumer traffic?
- **ALL TOPICS MESSAGES IN**: Messages per second?
- **PARTITION COUNT**: Many partitions assigned to a broker?
- **LEADER COUNT**: How many pertitions is this broker a leader for?
- **OFFLINE PARTITIONS**: Any brokers without leader?
- **REQUEST METRICS**: Number of requests to broker?

## Monitoring in Java

In Java, the main monitoring metrics are related to *garbage collection* (GC). This includes:

- **CollectionCount**: # of GC cycles
- **CollectionTime**: Time spent in GC cycle



## Common Producer Metrics

Common producer metrics include:

- **response-rate** (global and per broker): Responses (acks) received per second. Sudden changes in this value could
  signal a problem, though what the problem could be depends on your configuration.
- **request-rate** (global and per broker): Average requests sent per second. Requests can contain multiple records, so
  this is not the number of records. It does give you part of the overall throughput picture.
- **request-latency-avg** (per broker): Average request latency in ms. High latency could be a sign of performance
  issues, or just large batches.
- **outgoing-byte-rate** (global and per broker): Bytes sent per second. Good picture of your network throughput. Helps
  with network planning.
- **io-wait-time-ns-avg** (global only): Average time spent waiting for a socket ready for reads/writes in nanoseconds.
  High wait times might mean your producers are producing more data than the cluster can accept and process.

## Common Consumer Metrics

Some importent consumer metrics include:

- **records-lag-max**: Maximum record lag. How far the consumer is behind producers. In a situation where real-time
  processing is important, high lag might mean you need more consumers.
- **bytes-consumed-rate**: Rate of bytes consumed per second. Gives a good idea of throughput.
- **records-consumed-rate**: Rate of records consumed per second.
- **fetch-rate**: Fetch requests per second. If this falls suddenly or goes to zero, it may be an indication of problems
  with the consumer.

## Diagnosis

To diagnosis a unbalanced load / under-replicated parititons, consider the following metrics:

- **Partition and leader partition count**
- For all topics:
    - Messages **in** rate
    - Bytes **in** rate
    - Bytes **out** rate

The numbers will be even accross all brokers. If not, you will need to **move partiitons**, by using
the `kafka-reassign-partitions.sh` command.

Other problems with your brokers can include hardware failures; a broker's ability to serve requests. Metrics to
investigate is:

- **CPU** utilization
- **Inbound and outbound** network throughput
- **Disk average wait time**
- **Disk percentage utilization**

Analyze these as the traffic increases. For cluster use, monitor the **all topics bytes in rate** metric. 


