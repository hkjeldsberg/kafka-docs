# Confluent kSQL

kSQL/ksqlDB/KSQL is a database project meant to help create stream processing applications together with Kafka,
providing
SQL-like interface on top of Kafka Streams. Hence, many Streams operations can be performed using kSQL, including

- Data transformations
- Aggregations
- Joins
- Windowing
- Modeling data with streams and tables

In contrast to SQL, kSQL queries run continuously until stopped, streaming data in real time.

## kSQL Configuration

Similar to `server.properties`, ad `ksql.properties` may contain the following properties:

```console
bootstrap.servers=zoo1:9092
ksql.streams.state.dir=/tmp/kafka-streams
```

## Connecting to kSQL server

Connect to kSQL server using:

```console
(sudo) ksql
```

## Using kSQL – Commands

Commands are similar to SQL, but they query data from Kafka streams and tables.

Set configuration:

```console
SET 'auto.offset.reset' = 'earliest';
```

List topics:

```
SHOW TOPICS;
```

Print records in a topic (from beginning):

```
PRINT '<topic-name>' FROM BEGINNING;
```

Create a stream from a topic:

```
CREATE STREAM <name> (<fields>) WITH (kafka_topic='<topic>', value_format='<format>');
```

Here, `value_format` is commonly `DELIMITED`, while fields could be `(employee_id INTEGER, name VARCHAR)`.
Similarly, a table can be created with `CREATE TABLE`, which requires setting an ID using e.g. `key='employee_id'`.

Aggregate data:

```
SELECT sum(<field>) FROM <table or stream> GROUP BY <field>;
```

for example:

```console
SELECT sum(vacation_days) FROM my_test_stream GROUP BY employee_id;
```

Note that aggregations must have a `GROUP BY` statement.