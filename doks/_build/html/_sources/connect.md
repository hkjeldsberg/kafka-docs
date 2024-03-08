# Kafka Connect

Kafka Connect is a tool used to integrate Kafka against other systems, in real time. Completely configuration-based,
which can perform lightweight transformations on data. The system is fully de-coupled from the Kafka system.

## Sources and Sinks

A source connector **imports** data from an external system *into* Kafka, while a sink connector **exports** from Kafka
to an external system.

## Kafka Connect Components

Kafka Connect is composed of several components:

- **Connector**: Defines the interaction between Kafka Connect and external technology
- **Converter**: Handles serializaiton and de-serialization of data, and plays a role in persistence of schemas
- **Transformer**: Optional components that perform lightweight transformations on the data, as it flows though Kafka
  Connect. Single message transformations (SMT).

## Using Kafka Connect

Creating and managin connectirs is performed though a REST API. The connector implementation depends on the application,
and is configured though a `connector.class` field. An example of creating a sink connector from Kafka to a file could
be as follows:

```console
curl -X POST http://localhost:8083/connectors \
-header 'Accept: */*' \
-header 'Content-Type: application/json' \
-data '{
     "name": "file_sink_connector",
     "config": {
         "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
         "topics": "connect_topic",
         "file": "/home/cloud_user/output.txt",
         "value.converter": "org.apache.kafka.connect.storage.StringConverter"
     }
}'
```

## Deploying Kafka Connect

Deploying Kafka Connect only applies if we're managing self-managed Kafka!
Kafka Connect runs under JVM as a process known as **worker**, which can execute multiple connectors.
**Tasks** are executed by workers, which are either **standalone** or **distributed**

### Distributed

Kafka Connect uses Kafka Topics to store state pretaining to connector config, status, ect. The topics are configured to
retain this information indefinetly – **compacted topics**. Connector instances are created and managed using the REST
API that Kafka Connect offers. Tasks are rebalanced to redistritubet the wrokload automatically, and crashed units will
lead to a new rebalancing. Essentially, a *Kafka Connect Cluster* consists of one or multiple *workers* (preferably two
or more), where the workers are responsbible for separate *tasks*. See figure below.

```{figure} figures/worker_dist.png
---
name: worker-dist
---
Multiple workers on multiple clusters in a Kafka Connect Cluster.
```

### Standalone
