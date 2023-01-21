# This document helps to understand the previliges and access the customer has using Kafka CLI

Please download the kafka libraries to execute below kafka cli commands.

https://kafka.apache.org/downloads - download the Binary downloads

## Prerequisite 
1. Create JKS truststore using Ford CA files (ford_ca.pem) using below command. Run this command for Ford CA file which you received in email.

```shell
keytool -import -trustcacerts -alias ford-ca -file <ford_ca.pem> -keystore truststore.jks -deststorepass <your_truststore_pass> -deststoretype pkcs12 -noprompt 
```
    
2. Create a property config file with below sample 
   1. Sample JaaS Configuration file: kafka-client-config-sample_unix.properties
```properties
      bootstrap.servers=<kafka-bootstrap-url:443>
      sasl.mechanism=PLAIN
      security.protocol=SASL_SSL
      sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
      username="<cds_id>" \
      password="<your_password>";
      ssl.truststore.location=<location of JKS trustore self_ford_truststore.jks>
      ssl.truststore.password=<JKS truststore password>
```


   2. Sample JaaS Configuration file: kafka-client-config-sample_windows.properties
```properties
   bootstrap.servers=kafka.pp01.kafka.ford.com:443
   sasl.mechanism=PLAIN
   security.protocol=SASL_SSL
   sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="<CDSID>" \
   password="<CDSID-PASSWORD>";
   ssl.truststore.location=<location of JKS trustore self_ford_truststore.jks>
   ssl.truststore.password=<truststore_password>
```

### Linux, Unix, MAC users will have to use .sh. 
For example: **kafka-topics.sh, kafka-console-producer.sh**


### Windows users will have to use .bat  
For example: **kafka-topics.bat, Kafka-console-producer.bat**


## Below are the CLI commands the customer can execute with their current permissions

| FIM Group | Allowed Permission |
|-----------|--------------------|
|Admin Fim Group (e.g kafka-53532-adm)| Create Topic, Describe Topic, Delete Topic|
|Publisher Fim Group (e.g kafka-53532-pub)| Producer record to Topic|
|Subscriber Fim Group (e.g kafka-53532-sub)| Consumer from Topic, List Consumer Group, Describe Consumer Group|


### 1. Create Topic - Customers could create the topic using below command

````shell
kafka-topics.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--topic <topic-name> \
--create --replication-factor 3 --partitions 6
````

### 2. Describe the Topic - Customers could describe the topic using below command to see the topic configurations

````shell
kafka-topics.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--topic <topic-name> \
--describe
````

### 3. Produce data to the topic - Customers could produce or send message to the topic using below command

````shell
kafka-console-producer.sh --bootstrap-server <kafka-bootstrap-server:443> \
--producer.config=<config.properties> \
--topic <topic-name>
````

### 4. Consume data from the topic - Customers could consume or receive  message from the topic using below command

````shell
kafka-console-consumer.sh --bootstrap-server <kafka-bootstrap-server:443> \
--consumer.config=<config.properties> --topic <topic-name> \
--group <consumer-group>
--from-beginning
````

### 5. List consumer groups - Customer could list the consumer groups using below command

````shell
kafka-consumer-groups.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--list --timeout 600000
````


### 6.	Describe Consumer group - Customer could view the current configurations of the topic consumer group using below command

````shell
kafka-consumer-groups.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--group <consumer-group-name> --describe --timeout 600000
````

### 7.	Delete topic - Customers could delete the topic using below command

````shell
kafka-topics.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--topic <topic-name> –-delete
````

## Customers will not have the necessary permission to excute the commands below

Customer will not be able to alter topic configuration, delete consumer offsets or consumer group.

### 8.	Alter topic configs - Customer could not alter or modify the topic configuration using Kafka CLI - Topic Autorization will fail

#### Customers will have to raise a request to the Kafka service team

````shell
kafka-topics.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--topic <topic-name> --alter --partitions 4
````

### 9.	Delete offsets of topic from the consumer group - Not authorized for customers

#### Customers will have to raise a request to the Kafka service team

````shell
kafka-consumer-groups.sh --bootstrap-server <kafka-bootstrap-server:443> \
--delete-offsets \
--command-config=<config.properties> \
--group <consumer-group-name> --topic <topic-name> --timeout 600000
````

### 10. Adding retentions - Customer could not add retentions to the topic using Kafka CLI - Topic Autorization will fail

#### Customers will have to raise a request to the Kafka service team

````shell
kafka-configs.sh --bootstrap-server <kafka-bootstrap-server:443> \
--command-config=<config.properties> \
--entity-type topics  --alter --add-config retention.ms=100 --entity-name <topic-name>
````

### 11. Monitor the Topic Health

#### Unavailable partition (offline partition)
```shell
bin/kafka-topics.sh --bootstrap-server kafka.pp01.kafka.ford.com:443 \
--unavailable-partitions --describe \
--command-config=<config.properties>
```

#### Under replication partition
````shell
bin/kafka-topics.sh --bootstrap-server kafka.pp01.kafka.ford.com:443 \
--under-replicated-partitions --describe \
--command-config config/kafka-client-admin.properties
````

