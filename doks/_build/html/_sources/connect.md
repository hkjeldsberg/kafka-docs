# Kafka Connect

Kafka Connect is a tool used to integrate Kafka against other systems, in real time.

## Sources and Sinks

A source connector **imports** data from an external system *into* Kafka, while a sink connector **exports** from Kafka
to an external system.

## Using Kafka Connect

Creating and managin connectirs is performed though a REST API. The connector implementation depends on the application,
and is configured though a `connector.class` field. An example of creating a sink connector from Kafka to a file could
be as follows:

```console
curl -X POST http://localhost:8083/connectors \
-H 'Accept: */*' \
-H 'Content-Type: application/json' \
-d '{
     "name": "file_sink_connector",
     "config": {
         "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
         "topics": "connect_topic",
         "file": "/home/cloud_user/output.txt",
         "value.converter": "org.apache.kafka.connect.storage.StringConverter"
     }
}'
```