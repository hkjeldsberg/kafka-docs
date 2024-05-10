# Confluent Schema Registry and Data Types

The schema registry handles schemas, which contain information of the data structure of the log/record to be either read
or written to Kafka. The schema consists of **metadata** that describes a complex data format, and expected **fields**
and their data types; key-value format following a `JSON` format. One of these schemes is  **Apache Avro** or Avro, used
to define and use schemas. To store and access these schemas, a separate registry known as the **Confluent Schema
Registry** is used.

To create schemas, *producers* can register schemas with the registry and then use the schemas to serialize data.
Similarly, *consumers* can download schemas and use it to deserialize the data.


## Creating an Avro schema
A sample Avro schema is represented in `JSON`:

```json
{
  "namespace": "<namespace>",
  "type": "record",
  "name": "<schema name>",
  "fields": [
    {
      "name": "<field name>",
      "type": "<field type>",
      "default": "<some default value>"
    }
  ]
}
```

An example schema could be: 

```json
{
  "namespace": "com.example.project",
  "type": "record",
  "name": "Person",
  "fields": [
    {
      "name": "id",
      "type": "int"
    },
    {
      "name": "first_name",
      "type": "string"
    },
    {
      "name": "email",
      "type": "string",
      "default": "" 
    }
  ]
}
```
The `default` value needs to be set if field is considered **optional**, which is relevant when making changes to the schema. 

## Using Avro schema in Java
In properties add the `KafkaAvroSerialiser`:

```java
import java.util.Properties;

Properties props = new Properties();
props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, KafkaAvroSerializer.class);
props.put(AbstractKafkaAvroSerDeConfig.SCHEMA_REGISTRY_URL_CONFIG, "http//localhost:8081");
// Person will now be a generated POJO through Avro/Kafka
```


## Managing Schema Evolution

The registry has a *schema compatibility checking feature*, which determines which changes can or cannot be made to a
schema. If a schema is registered with invalid changes, an **error** will be raised. The different changes are
controller by the schema's  **compatability type**, one of the following:

- **BACKWARD**
- **BACKWARD_TRANSITIVE**
    - Both allow **deletion** of fields and *addition* of **optional fields**
- **FORWARD**
- **FORWARD_TRANSITIVE**
    - Both allow **addition** of fields and *deletion* of **optional fields**
- **FULL**
- **FULL_TRANSITIVE**
    - Both allow **addition** and **deletion** of *optional* fields
- **NONE**
    - All changes are accepted

## Adding Avro Schemas

Schemas can be added to Java projects by using the *Avro Gradle* plugin to help locate and generate a Java class from
it. Then, as a producer, the schema can be loaded and records can be produced to Kafka:

```java
final Properties props = new Properties();
props.put(AbstractKafkaAvroSerDeConfig.SCHEMA_REGISTRY_URL_CONFIG, "http://localhost:8081");

// Create a producer with automatically generated Avro class "Person"
KafkaProducer<String, Person> producer = new KafkaProducer<String, Person>(props);

// Create and send a person
Person kenny = new Person(125745, "Kenny", "Armstrong", "kenny@linuxacademy.com");
producer.send(new ProducerRecord<String, Person>("employees", kenny.getId().toString(),
kenny));

producer.close()
```

Similarly, the Schema Registry can be used by the consumers:

```java
package com.linuxacademy.ccdak.schemaregistry;
public static void main(String[] args){
    final Properties props = new Properties();
    ...
    props.put(AbstractKafkaAvroSerDeConfig.SCHEMA_REGISTRY_URL_CONFIG, "http://localhost:8081");
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, KafkaAvroDeserializer.class);
    props.put(KafkaAvroDeserializerConfig.SPECIFIC_AVRO_READER_CONFIG, true);
    
    // Create consumer with Avro created class "Person"
    KafkaConsumer<String, Person> consumer = new KafkaConsumer<>(props);
}
```
