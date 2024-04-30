# Advanced Producers

## Idempotency

By setting certain properties, we can ensure that messages reach the Kafka cluster and Consumer from the producers. One
of these properties is **Idempotent Producers**, controlled by the **enable.idempotence** property. By default, this is
a *low* priority property. Enableing this will ensure we (1) do not lose messages, and (2) do not duplicate messages.
Enabling idempotency attaches a **produce-request-ID** to the acknowledgement – which the broker can detect to avoid
duplicates.

To enable this in Java, add the following configuration:

```java
properties.setProperty(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, "true");

// Controllers how many partitions needs to recieve the message (all)
properties.setProperty(ProducerConfig.ACKS_CONFIG, "all");

// If error message never reaches producer – Retry sending messages from producer 
properties.setProperty(ProducerConfig.RETRIES_CONFIG, Integer.toString(Integer.MAX_VALUE)); 

// Controllers number of messages the producer will send without getting a response
properties.setProperty(ProducerConfig.MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION, "5");
```

## Batch Compression

As an alternative to sending individual messages: sending from the producer in **batch**. Message compression will
increase the throughput of the messages. Compression in configured on the *Producer* side. Disadvantage: requires more
CPU power, but efficiency gained outweighs this.

In the Producer the **sender** communicates with a **RecordAccumulator** on how to send the request over to a Kafka
Cluster. The sender is only aware of the cluster's metadata, which lets it find the broker to write to.

The larger the batch, the better the compression. If it fails, we avoid duplication and loss with idempotency (see
above).

To add compression type and batch configuration in Java:

```java
// Set compression type (gzip, tar, snappy, lz4)
properties.setProperty(ProducerConfig.COMRPRESSION_TYPE_CONFIG, "snappy");

// Batch size
properties.setProperty(ProducerConfig.BATCH_SIZE_CONFIG, Integer.toString(32*1024)));

// Time to wait for additional messages before sending _NEXT_ batch
properties.setProperty(ProducerConfig.LINDER_MS_CONFIG, "20");
```

## Serializers

**Serializers** are used to control the format in which data is stored when sent from a producer to a cluster. Recall;
each message has a time stamp to create a ordered group. A pre-built serializer is the `StringSeralizer`. Custom
serialization can be used to translate data in a format that can be *stored, transmitted, and retrieved*. Alternative, a
popular serializer is the `KafkaAvroSerializer`.

## Producer Buffer Memory

What happens when the producer is producing messages faster than the broker can handle? Or if there is some broker
failure? Where do the messages go?

Messages will in this case be sent to a **buffer memory** on the producer. Buffer size is by default 32MB, which will be
tuned as the producer produces messages. A configuration `max.block.ms` controls the time before an exception is raised
if the broker is not responding (default 60 seconds).