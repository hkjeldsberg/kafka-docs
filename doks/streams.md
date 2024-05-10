# Kafka Streams

Kafka Streams is an API where both the input and output is stored in Kafka topics.
Streams are a *sequence of events*; real-time events. Example of events:

- Transactions
- Stock trades
- Deliveries
- Network events
- Moves in a game

## Stateless and Stateful transformations

Tranformations that are **stateless** do not require a special data store. Transformation that are **stateful** require
a special data store to store the current **state** for the stream.

### Stateless transformations

Some stateless transformations inlcude:

- `.branch`, splitting a stream into multiple streams
- `.filter`, remove records based on a criteria or condition
- `.flatMap`, converting input into any number of output records
- `.foreach`, to perform some operation on each record. Prevents further processing
- `.groupBy`, groups the stream by a new key
- `.groupByKey`, groups by existing key
- `.map`, converts and modify one and one record
- `.merge`, used to merge two streams
- `.peek`, perform stateless operation (non-terminal)

### Stateful transformations

A stateful transformation in Kafka is the aggregation of records, performed by the `.aggregate` stream function. Types
of aggregation include:

- `.aggregate`, a generalized aggregation
- `.count`, counts the number of records
- `.reduce`, combines grouped records to reduce the number of input records based on a value or condition

Another stateful transformation is join operation, which combines two input streams. Different types of join can be
used, including:

- `.join`, an inner join (intersection)
- `.leftJoin`, joining "left" stream and matching records from the "right" stream
- `.outerJoin`, combining two streams with all records from both streams. (union)

## Note on Co-Partitioning

When joining streams, the data must be co-partitioned:

- Same number of partitions for input topics.
- Same partitioning strategies for producers.

## Join operations

The following join operations are available in Kafka streams:

- Inner join
- Left join
- Outer join

Join operations are performed between two streams from two topics:

```kotlin
val left = builder.stream(topicLeft)
val right = builder.stream(topicRight)

val innerJoined = left
    .join(
        right,
        { leftVal, rightVal -> "left=" + leftVal + ", right=" + rightVal },
        JoinWindows.of(Duration.ofMinutes(5))
    ) // Windowing
    .to(topicJoined) // Output topic
```

Similarly, a `.leftJoin` and `.outerJoin` can be defined. The values will join on the topics keys.

## Windowing

One way to perform operations sequentially is by using **windowing**. This operation allows subdivision of groups into "
time buckets", aggregating them bit by bit. Different window types exist:

- **Tumbling**: No overlap, no gap
- **Hopping**: Time-based, but can have overlap *and/or* gaps
- **Sliding**: Dynamically based, only used for *joins*. Defined by the length of time between timestamps of any two
  records. No gaps, but
  overlap.
- **Session**: Dynamic windows formed around activity based on the timestamps. Will contain *gaps* due to inactivity /
  idle time.

Late-Arriving Records is records that fall into a time window received after the end of that window's grace period.
These records can be processed by adjusting the **retention period** for a window.
Any records arriving after this period will not be processed.

Setting a tumbling and hopping windowing can be controlled by the `.advanceBy(Duration.)`:

```kotlin
import java.time.Duration

// New window will be started after 12 seconds => Hopping time window
val windowedStream = stream
    .groupByKdey()
    .windowedBy(TimeWindows.of(Duration.ofSeconds(10)).advanceBy(Duration.ofSeconds(12))) 
```

## Time in Streams

*Time* in the context of streams is used in operations such as *windowing*, and define time boundaries. These include:

- **Event time**: Time when event was *created* at the source.
- **Ingestion time**: Time when the event was *stored* in a topic partition by a Kafka broker.
- **Processing time**/Wall-clock-time: Time when the event started to be *handled/processed* by the stream processing
  application.

Processing time can happen days after the event actually happened. Time zones should be standardized.

## Streams vs Database/Tables

There are some notable differences, including:

- Streams keep a history of changes
- No deleting a stream
- A table can be converted to a stream (or other way)
- Streams are preferred for transactions or real-time events / logs


## Streams Design Patterns

Depending on the goal and purpose of the data, sevaral stream design patterns are available. Patterns:

- **Single event processing**: Each event is handled independently. No state is needed.
- **Local state processing**: May include storing min and max values; need to store some state. Suffers from increased
  memory usage.
- **Multiphase processing**: Partially uses local state, but kicks over to a new partition for aggregating part of the
  incoming data.
- **External processing**: When data is external to a stream and performs an external lookup. May cause latency issues.
- **Windowed join**: Design where two event streams are matched within a *time window*.
- **Out of Sequence events**: For handling events arriving at the wrong time.

## Stream Frameworks

What framework to use will depend on the type of application.

- **Ingest**: Try Kafka Connect.
- **Low millisecond**: Request/Response system.
- **Real-time data analytics**:  Performing complex aggregations for insight.
- **Asyncronous Microservices**:Rquires local state caching events.