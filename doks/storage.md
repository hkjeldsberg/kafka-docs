# Kafka Storage

## File formats and Indexes

Data is formated in a seleceted directory, determined by the `log.dirs` configuration in `server.properties`. As more
messages are produces, this directory will increase, and files (log segments) will duplicate over time. For instance, a
partition folder named `test-0` will typically contain:

```console
$ ls /tmp/kafka-logs-0/test-0 
-rw-r--r--@  1 wheel    10M Mar 15 12:02 00000000000000000000.index
-rw-r--r--@  1 wheel   557B Mar 15 14:20 00000000000000000000.log
-rw-r--r--@  1 wheel    10M Mar 15 12:02 00000000000000000000.timeindex
-rw-r--r--@  1 wheel    56B Mar 15 12:02 00000000000000000004.snapshot
-rw-r--r--   1 wheel    16B Mar 15 14:19 leader-epoch-checkpoint
-rw-r--r--@  1 wheel    43B Mar 13 15:49 partition.metadata
```

In order to enable *log compaction*, pass an additional configuration flag when creating a
topic: `--config cleanup.policy=compacy`, with parameters `--config min.cleanable.dirt.ratio` and `--config segment.ms`
determining how often compaction is performed (frequency and segment creation time).

## File/Storage Administration

Default retention period is one week, and default retention size is 1Gb for each partition. As more messages are
produced, segments accumulate and contain individual offsets. Thus, only one segment will be active, known as the **
active segment**. An active segment is never deleted. In addition, partitions are distributed to brokers through a **
round-robin** distribution. Example:

- Partition 0 -> Broker 1
- Partition 1 -> Broker 2
- Partition 2 -> Broker 3

## Storage Structures

Kafka supports several storage structures, including

- Lambda architecture: Batch processing and Operational workloads – Real-time *and* historical view
- Kappa architecture: V1 and V2 in parallel – Helps with migration or transitinon to continuous jobs
- Multiple consumption: Multi-cluster – Replicates clusters for scale or ingesting from different Kafka clusters

