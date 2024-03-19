# Advanced Consumers

## Reading Duplicate Messages

The consumer controlls the throughput by pulling messages from the broker. With this control, the consumer may controll
several variables:

- `fetch.min.bytes` and`fetch.max.bytes`: Min and max batch size of the request from the consumer
    - Defaults: 1 byte and 50 MB.
- `max.poll.records`: Max number of messages recieved by the consimer
    - Default: 500
- `max.partition.fetch.bytes`: Max amount of data the broker will return to the consumer
    - Defaukt: 1 MB

In addition, pulling is controller by the `enable.auto.commit` variable. If set to `true` (default), it will sync
message batches automatically. If `false`, you will need to manually commit the offsets, giving more control to the
user.

## Tracking Offsets

Offset are message *indecies* in Kafka. Consumers use Kafka track positions via offsets. The action of updating the
current position in the current partition is called a **commit**. Hence, a consumer produces a message to
the `__consumer_offsets` topic (working as a producer) to keep track of the offset. This offset-topic will be used if a
consumer traches and triggers a re-balance. In theory, the offset value can grow infinetley and is of type **long** data
type (minimal risk of overflow).

Consumers read from the leder partition, with each their offsets (which may be the same). A group coordinater lets the
consumer know how to tell them apart.

Using a consumer group is recommended, and is controller by a `groupId`. Additional consumer groups would have consumers
with no consumer offsets. In Java this is configured as follows:

```java
properties.setProperty(ConsumerConfig.GROUP_ID_config, groupID);
```

## Partition Assignment and Re-balancing

What happens when a new consumer is added? Consumers of the same consumer group start pulling from partitions already
consumer from. Re-assignment happens also when topics are modified.

As soon as a consumer has ownership of a partition, it will send out **heartbeats**. Either when it *pulls* messages or
when it commits records it has consumed. If no heartbeat is registered it will be considered dead, and the group
coordinated will trigger a *re-balance*.

The first consumer joining the consumer group becomes the *group leader*, before assigning remaining consumers to
partitions. The group leader then sends a list of assignments to the group coordinater sending info to all consumers.
This repeats for each re-balance, and is automatic. Alternatively, it can be performed manually.

Typee of re-balancing include **round-robin** (random, equally distributed, unordered) and **range** (equally
distributed and ordered).

After a consumer is initiated, it needs to be **subscribed** to a topic. Alternativeely, it can be **assigned** to one
or multiple partitions, see the following Java code.

```Java
// Subscribe
consumer.subscribe(Array.asList(topic));

//  Assign
consumer.assign(Array.asList(partition0, partition1));
```

## Consumer Group Coordinater

One of the broker becomes the consumer group coordinater. The coodinater has the following tasks:

- Reach out to all consumers in a consumer group to assess for a heartbeat
- Assign appropriate partitions when a new consumer is added
- Keeps track of offsets and offset commits

An example of the workflow is shown in the diagram below.

```{figure} figures/coordinator.png
---
name: coord
---
The first consumer consumes from partitions on broker 1 and 3, while the second consumer consumes from partitions on broker 2. This is coordinated by broker 3 which works as the group coordinator.
```

Reasons for *not* adding too many consumers:

- Excess consumers will be idle = Wasted resources
- Whole consumer group will be down during the re-balancing period

To figure out when a consumer is down or dead, assess the following metrics:

- Heartbeat interval (`heartbeat.interval.ms`)
- Amount of time consumer is out of contact with coordinator (default: 3 seconds)
- `session.timout.ms` is how long a consumer can go without sending a heartbeat 