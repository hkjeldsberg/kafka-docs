(cmd-imp)=

# Kafka CLI commands

## Starting Zookeeper and Kafka

To start a Zookeeper instance with config `zookeeper.properties`:

```console
zookeeper-server-start config/zookeeper.properties
```

To start a Kafka server with config `server.properties`:

```console
kafka-server-start config/server.properties
```

## Topics

Connect to a Kafka server and create a topic using:

```console
kafka-topics --bootstrap-server kafka:9092 --create  --topic <topic-name>  --partitions $PART  --replication-factor $REP
```

Then view active Kafka topics using the `--list` command: `

```console
kafka-topics --bootstrap-server kafka:9092 --list
```

## Producers

To produce data use the `kafka-console-producer` command to add data (optionally with a key):

```console
kafka-console-producer --broker-list kafka:9092  --topic sample-topic --property parse.key=true  --property key.separator=,
```

## Consumers and Consumer Groups

View the produced data using the `kafka-console-consumer` command:

```console
$ kafka-console-consumer --bootstrap-server kafka:9092 --topic sample-topic --from-beginning --property print.key=true
```

Similarly, to view consumer groups, we can use `kafka-consumer-groups`:

```console
kafka-consumer-groups --bootstrap-server kafka:9092 --group vp-consumer --describe
```

or to continuously watch the process, use `watch`:

```console
watch kafka-consumer-groups --bootstrap-server kafka:9092 --group vp-consumer --describe
```

## Kafka ACL (Access Control List)

Add an ACL to existing user `myuser`:

```console
kafka-acls --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:myuser --operation write --topic acl-test
```

## Kafka Connect

To start a connector (standalone source):

```console
./bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties
```