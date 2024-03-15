# Metrics and Monitoring

Kafka metrics are accessed using `JMX`, accessable by passing a `JMX`-option via the `KAFKA_JMX_OPTS` env variable.

```console
KAFKA_JMX_OPTS="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false -
Djava.rmi.server.hostname=localhost"
```

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