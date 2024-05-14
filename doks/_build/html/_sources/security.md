# Security, Authentication and Authorization

## Security in Kafka

SSL and SASL authentication is supported between Clients (Producers and Consumers) and Brokers, between Brokers, and
between Brokers and Tools. Different SASL methods are available (GSSAPI, PLAIN, SCRAM-SHA-(256/512), OAUTHBEARER with
JWT).
Authentication and authorization is supported, but is **disabled** by default. Creating a SSL key can be performed using
the Java *keytool* utility, or similarly in other languages.

## Configuring TSL encryption

Using TLS encryption will allow encryption of traffic between clients and Kafka – as a method to defend against
attacks (man-in-the-middle). This will require certificate authority and certificate files for the Kafka Brokers, which
can be configured like this in `server.properties`:

```console
listeners=PLAINTEXT://<hostname>:9092,SSL://<hostname>:9093

ssl.keystore.location=/var/private/ssl/server.keystore.jks
ssl.keystore.password=<keystore password>
ssl.key.password=<broker key password>
ssl.truststore.location=/var/private/ssl/server.truststore.jks
ssl.truststore.password=<trust store password>
ssl.client.auth=none // Client auth off
```

For the producer and consumer, settings related to SSL can be placed in a `client-ssl.properties` file and passed as a
console argument when running the consumer/producer:

```console
kafka-console-consumer [args] --consumer.config client-ssl.properties
```

## Client authentication

In the TLS/SSL configuration above, client authentication was turned off. To handle client authentication, we can use
client certificates or other approaches. With client certificates, we will
need to change the `ssl.client.auth` field in `server.properties` to:

```console
ssl.client.auth=required
```

### Authentication with Mutual TLS

For additional security we may perform authentication with mutual TLS with client certificates. This involves:

- Generating a client certificate
- Sign in with Certificate Authority
- Enable SSL client authentication
- Configure a client to use the client certificate

To the `ssl.client.auth` add the following fields:

```console
ssl.keystore.location=/PATH
ssl.keystore.password=<client keystore pass>
ssl.key.password=<client key pass>
```

## ACL authorization

Authorization is controlled by *access control lists* (ACL) in Kafka, consisting of the following components:

- Principal – the user
- Allow/Deny
- Operation – actions the user can perform
- Host – IP address(es) of hosts connecting to the cluster
- Resource Pattern – matching one or more resources (topics, partitions, brokers, ect. )

To enable ACL auth, use the following template of `server.properties`:

```console
authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer
super.users=User:admin // TYPE:NAME
allow.everyone.if.no.acl.found=true
ssl.principal.mapping.rules=RULE:^CN=(.*?),OU=.*$/$1/,DEFAULT // Parses username from client certificate
```

### Creating an ACL

Authorization can be managed using the `kafka-acls` command line tool. To configure authorization with specified
principal allowed to perform action:

```console
kafka-acls --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:[USERNAME] --operation all --topic [TOPIC_NAME]
```

The operation `--operatiomn all` gives full access.

