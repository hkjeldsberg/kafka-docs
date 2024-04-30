# Advanced Topics

## Topic Design

What do we need to consider when designing topics?

- Data accuracy
    - Making sure that events that must be ordered end up in same partition (and same topic)
    - Using key'ed records needing to be ordered, patition setup will be essential
    - In contrast, search events may not need to be ordered
- Popularity of events
    - E.g. searches for tickets > Any other request
    - Consumers time spent filtering out mass of events
- Amount of data to process
    - Will we need multiple consumers to prosess this data?
    - Easy solution: Increase number of partitions, but this will require more resources
    - Hint: Start with small partitions

## Topic Options

Topic options or parameters are additional parameters provided to the `kafka-topics` command. Only required parameter
is `--bootstrap-server`, and an *action*, e.g.

- `--list`
- `--create`
- `--describe`

To make sure you do not create topics on accident, set `auto.create.topics.enable=false`.

Altering the topics can be performed with the `--alter` flag, although this is only recommended for partitions without a
key, or when there is no data. If the topics has a key, the partition logic or ordering of the messages will be
affected.

Adding and deleting configuration can be performed using `--add-config <key>=<value>` and `-delete-config <key>`
options.

## Topic Alterations

Topic/Log compaction is different from retention; here the goal is to make sure that the latest value exists, and is
controlled by the `--config cleanup.policy=compact` option. When a topic is marked for compaction, a single log can be
observed in either a *clean* or *dirty* state.

- *Clean state*: Messages that have been though compaction before (duplicates do not exist)
- *Dirty state*: Messages that have **not** been though compaction before (duplicates exist)

A partition is compacted when the ratio between dirty and total records is larger than `min.cleanable.dirty.ratio` and
by either:

- AND `min.compaction.lag.ms` is reached
- OR `max.compaction.lag.ms` is reached

When data is sparse, it is recommended to adjust the requency of log rotation. Using the option `log.roll.hours=24` will
cause segments rotate about once a day (broker configuration).
