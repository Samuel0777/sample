# Monitor Consumer Offset lag

Consumer lag indicates the lag between Kafka producers and consumers. If the rate of production of data far exceeds the rate at which it is getting consumed, consumer groups will exhibit lag. 
It can be understood very succinctly as the gap between the difference between the latest offset and consumer commit offset.

## Option1: Monitor Consumer Offset Lag via Kafka Admin CLI

You can monitor offsets by using the Kafka Admin API and the associated CLI, which enables accessing lag information programmatically.

1.	Create a client properties file to hold the Kafka client configuration.

client.properties file:

```properties
bootstrap.servers=kafka.pp01.kafka.ford.com:443
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
username="<username>" \
password="<password>";
ssl.truststore.location=/Users/Documents/kafka/config/truststore.jks
ssl.truststore.password=<trust-store-password>

```
2.	Run below Kafka cli command to get the list of all consumers part of consumer group and their offset details per partition level. We need to pass client properties with the --command-config argument and consumer group with –group argument

To List Consumer group:
```sh
bin/kafka-consumer-groups --bootstrap-server kafka.pp01.kafka.ford.com:443 \
--command-config config/kafka-client.properties \
--list --timeout 600000
```

Describe consumer group
```sh
./kafka-consumer-groups.sh --bootstrap-server kafka.pp01.kafka.ford.com:443 \
--command-config=client.properties --describe --group <consumer-group> --timeout 600000
````
Sample query
```sh
./kafka-consumer-groups.sh --bootstrap-server kafka.pp01.kafka.ford.com:443 --command-config=client.properties --group servicebus-sink-consumer --describe
````

Result:

    GROUP                          TOPIC                     PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID                                      HOST            CLIENT-ID
    a12345-dev-test-group a12345-dev-test 2          10              10              0               consumer-13-b6135eda-ac10-4269-88c3-41abcc547f56 /172.24.6.20    consumer-13
    a12345-dev-test-group a12345-dev-test 1          11              11              0               consumer-13-b198375a-3255-4fb7-937c-2ba62244798e /172.24.6.20    consumer-13
    a12345-dev-test-group a12345-dev-test 0          9               9               0               consumer-13-6950b83c-cb39-4486-94c2-55aca1942bf9 /172.24.8.7     consumer-13

Query will provide partition level consumer offset and lag details for each consumer in the consumer group

    Lag = (LOG-END-OFFSET) – (CURRENT-OFFSET)

## Option 2: Get consumer lag using Java Client

Another way to get this data programmatically is by using java client. Consumer group lag is calculated as follow: LAG = LOG_END_OFFSET - COMMITTED_OFFSET

Refer the example code in the git repo for details on java client to calculate the consumer offset lag.

https://github.com/gwenshap/kafka-examples/blob/master/AdminClientExample/src/main/java/org/example/AdminClientExample.java#L156-L189

### code snippet

````java
// Get offsets committed by the group
Map<TopicPartition, OffsetAndMetadata> offsets =
admin.listConsumerGroupOffsets(CONSUMER_GROUP)
.partitionsToOffsetAndMetadata().get();

Map<TopicPartition, OffsetSpec> requestLatestOffsets = new HashMap<>();
Map<TopicPartition, OffsetSpec> requestEarliestOffsets = new HashMap<>();
Map<TopicPartition, OffsetSpec> requestOlderOffsets = new HashMap<>();
DateTime resetTo = new DateTime().minusHours(2);

// For all topics and partitions that have offsets committed by the group, get their latest offsets, earliest offsets
// and the offset for 2h ago. Note that I'm populating the request for 2h old offsets, but not using them.
// You can swap the use of "Earliest" in the `alterConsumerGroupOffset` example with the offsets from 2h ago
for(TopicPartition tp: offsets.keySet()) {
requestLatestOffsets.put(tp, OffsetSpec.latest());
requestEarliestOffsets.put(tp, OffsetSpec.earliest());
requestOlderOffsets.put(tp, OffsetSpec.forTimestamp(resetTo.getMillis()));
}

Map<TopicPartition, ListOffsetsResult.ListOffsetsResultInfo> latestOffsets =
admin.listOffsets(requestLatestOffsets).all().get();

for (Map.Entry<TopicPartition, OffsetAndMetadata> e: offsets.entrySet()) {
String topic = e.getKey().topic();
int partition = e.getKey().partition();
long committedOffset = e.getValue().offset();
long latestOffset = latestOffsets.get(e.getKey()).offset();

    System.out.println("Consumer group " + CONSUMER_GROUP
    + " has committed offset " + committedOffset
    + " to topic " + topic + " partition " + partition
    + ". The latest offset in the partition is "
    + latestOffset + " so consumer group is "
    + (latestOffset - committedOffset) + " records behind");
}

````

Sample Output from the Java client:


    Consumer group a12345-devk-test-consumer1 has committed offset 2 to topic a12345-dev-gcpaviatrixazkafka partition 2. The latest offset in the partition is 2 so consumer group is 0 records behind
    Consumer group a12345-devk-test-consumer1 has committed offset 1 to topic a12345-dev-gcpaviatrixazkafka partition 0. The latest offset in the partition is 1 so consumer group is 0 records behind
    Consumer group a12345-devk-test-consumer1 has committed offset 1 to topic a12345-dev-gcpaviatrixazkafka partition 1. The latest offset in the partition is 1 so consumer group is 0 records behind
    Consumer group a12345-devk-test-consumer1 has committed offset 2108141 to topic a12345-dev-test partition 1. The latest offset in the partition is 2108141 so consumer group is 0 records behind
    Consumer group a12345-devk-test-consumer1 has committed offset 2096427 to topic a12345-dev-test partition 2. The latest offset in the partition is 2096427 so consumer group is 0 records behind
    Consumer group a12345-devk-test-consumer1 has committed offset 2108944 to topic a12345-dev-test partition 0. The latest offset in the partition is 2108944 so consumer group is 0 records behind
