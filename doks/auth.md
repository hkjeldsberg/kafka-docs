# Security, Authentication, Authorization in Kafka

## Security

Using TLS encryption will allow encryption of traffic between clients and Kafka – as a method to defend against
attacks (man-in-the-middle). This will require certificate authority and certificate files for the Kafka Brokers, which
can be configured like this:

```console
listeners=PLAINTEXT://<hostname>:9092,SSL://<hostname>:9093

ssl.keystore.location=/var/private/ssl/server.keystore.jks
ssl.keystore.password=<keystore password>
ssl.key.password=<broker key password>
ssl.truststore.location=/var/private/ssl/server.truststore.jks
ssl.truststore.password=<trust store password>
ssl.client.auth=none
```

## Client authentication

To handle client authentication, we can use client certificates or other approaches. With client cerificiates, we will
need to add the following field in `server.properties`:

```console
ssl.client.auth=required
```

## ACL authorization

Authorization is controlled by *access control lists* (ACL) in Kafka, consisting of the following components:

- Principal, the user
- Operation, actions the user can perform
- Host, IP address(es) of hosts connecting to the cluster
- Resource pattern, matching one or more resources (topics, partitions, brokers, ect. )

To enable ACL auth, use the following template of `server.properties`:

```console
authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer
super.users=User:admin
allow.everyone.if.no.acl.found=true
ssl.principal.mapping.rules=RULE:^CN=(.*?),OU=.*$/$1/,DEFAULT
```

Authorization can be managed using the `kafka-acls` command line tool.