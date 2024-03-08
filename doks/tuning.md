# Tuning producers and consumers

Tuning can be important to optimize and improve performance for you specific use case, and can be controller using a
variety of configuration options.

## Tuning producers

Common configuration options for producers include:

- **acks**: Is a number/value $\in$ [0,1, all / -1] determining when the borker will acknowledge the record
    - 0: Producer will not wait
    - 1: Record will be acknowledged when leader writes
    - all/-1: Record will be acknowledged only when the leader and *all* replicas have written
- **retries**: Number of times to retry a record
- **batch.size**: Maximum number of bytes in a batch (producers batch records sharing partition)

## Tuning consumers

Common configuration options for consumers include:

- **fetch.min.bytes**: The minimum amount of data to fetch in a request. If there is not enough data to satisfy this
  requirement, the request will wait for more data before responding. Set this higher to get better throughput in some
  situations at the cost of some latency.
- **heartbeat.interval.ms**: How often to send heartbeats to the consumer coordinator. Set this lower to allow a quicker
  rebalance response when a consumer joins or leaves the consumer group.
- **auto.offset.reset**: What to do when the consumer has no initial offset
    - latest: Start at the latest record.
    - earliest: Start with the earliest record.
    - none: Throw an exception when there is no existing offset data.
- **enable.auto.commit**: Periodically commit the current offset in the background. Use this to determine whether you
  want to handle offsets manually or automatically