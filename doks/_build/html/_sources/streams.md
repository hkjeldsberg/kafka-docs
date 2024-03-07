# Kafka Streams

## Stateless and Stateful transformations

Tranformations that are **stateless** do not require a special data store.

Transformation that are **stateful** require a special data store to store the current **state** for the stream.

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

One way to perform operations sequentially is by using **windowing**. This operation allows subdivision of groups into "
time buckets", aggregating them bit by bit. 

