# Confluent kSQL

kSQL/ksqlDB/KSQL is a database project ment to help create stream processing applications together with Kafka, providing
SQL-like interface on top of Kafka Streams. Hence, many Streams operations can be performed using kSQL, including

- Data transformations
- Aggregations
- Joins
- Windowing
- Modeling data with streams and tables

In contrast to SQL, kSQL queries run continously until stopped, streaming data in real time.

Some common commands include listing topics:

```
SHOW TOPICS;
```

Print records in a topic:

```
PRINT '<topic-name>';
```

Create a stream from a topic

```
CREATE STREAM <name> (<fields>) WITH (kafka_topic='<topic>', value_format='<format>');
```

Aggregate data:

```
SELECT sum(<field>) FROM <table or stream> GROUP BY <field>;
```