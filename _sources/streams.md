# Kafka Streams

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

## Windowing

One way to perform operations sequentially is by using **windowing**. This operation allows subdivision of groups into "
time buckets", aggregating them bit by bit. Different window types exist:

- **Tumbling**: No overlap, no gap
- **Hopping**: Overlap and/or gaps
- **Sliding**: Used for joins. Defined by the length of time between timestamps of any two records. No gaps, but
  overlap.
- **Session**: Dynamic windows formed around activity based on the timestamps. Will contain inactivity *gaps*

## Time in Streams

*Time* in the context of streams is used in operations such as *windowing*, and define time boundaries. These include:

- **Event time**: Time when event was *created* at the source.
- **Ingestion time**: Time when the event was *stored* in a topic partition by a Kafka broker.
- **Processing time**/Wall-clock-time: Time when the event started to be *handled/processed* by the stream processing
  application.

Processing time can happen days after the event actually happened. Time zones should be standardized.

## Streams vs Database

There are some notable differences, includingStreams:

- Streams keep a history of changes
- No delting a stream
- A table can be converted to a stream (or other way) 
