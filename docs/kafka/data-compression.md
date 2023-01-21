# How does compression work and how to choose the right algorithm
## Overview
By default, Kafka does not compress messages. However, this is often an easy win when optimising applications as, depending on the type of data, this can help increase message throughput by a great margin, for a reasonable CPU cost. This article describes the various mechanisms implemented by Kafka, and how to determine which algorithm is the optimal one for a given use case. This article does intend to compare compression algorithms with each other, just provide guidelines for benchmarking various algorithms for your own use case.

## Need for Message Compression
There can be the following reasons which better describes the need to reduce the message size:

1. It will reduce the latency and size required to send data to Kafka. 
2. It will reduce the bandwidth that will make users increase the net messages which are sent to the broker. 
3. It can lead to low cost when the data is stored in the Kafka via cloud platforms. It is because cloud services are paid. Therefore, it calculates the amount of data stored in Kafka. 
4. Message compression does not need any change in the configuration of the broker and consumer. 
5. Message compression does not need any change in the configuration of the broker and consumer. 
6. The reduced disk load will lead to fast read and write operations.

## Compression methods
In most cases, enabling compression (regardless of the algorithm) will have a positive impact on performance, as long as the server hosting the application is not already CPU-bound.
Different algorithms will have different performance profiles (CPU usage vs compression ratio). 

Kafka currently supports the following compression algorithms:
* GZIP (Zlib)
* LZ4
* Snappy
* ZStd (Since Kafka 2.1)

In most cases, GZIP and ZStd provide the best compression ratios, at a slightly higher CPU cost, while Snappy and LZ4 tend to reduce CPU cost at the expense of lower compression ratios. However, the actual result is very dependent on the actual type of load.

## Relationship between compression and batches 
Kafka (both clients and brokers) do not compress messages individually. All components actually compress batches which are sent. Thus, compression ratio will greatly depend on the batch.size and linger.ms settings. More precisely, the batch.size setting refers to the size of the batch after compression has been applied, meaning that more messages will be able to fit within a single batch. For example, while only 200 messages fit in a 16kb uncompressed batch, then 1000 messages might fit in a compressed batch (in the event of a 1:5 compression ratio).

If an application has been previously tuned for usage without compression, this means that these parameters might have to be reviewed once again after enabling compression.
Because the number of messages per batch will be different, the application might more easily reach linger.ms instead of the batch.size.
The number of messages per batch being higher, this also means that the number of network round-trips will be reduced (reducing impact from latency), and reducing network usage, leading to significant performance gains.
The actual gain will depend on the compression algorithm chosen. Because these depend on the workload and message format used by the application, performance tests should be performed in order to choose the correct compression algorithm. Compression performance can be tracked in producers' per-topic metrics, more precisely using the compression-rate metric.

## Enabling compression

### Producers
Compression can be enabled for producers using the compression.type setting.
For example, using kafka-console-producer-perf-test:
```shell
bin/kafka-producer-perf-test.sh --topic benchmark-1-1-zstd \
--num-records 15000000 \
--record-size 100 \
--throughput 15000000 \
--producer-props \
acks=1 \
bootstrap.servers=kafka-kf-1:9092,kafka-kf-2:9092,kafka-kf-3:9092 \
buffer.memory=67108864 \
compression.type=zstd \
batch.size=8196
```

#### Using the Java client:
Properties props = new Properties();

props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "kafka-1:9092,kafka-2:9092,kafka-3:9092");
props.put(ProducerConfig.ACKS_CONFIG, "all");
props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
props.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");

try (Producer<String, String> producer = new KafkaProducer<>(props)) {
// Your application code
}

Note that it is generally better to let producers handle compression, as enforcing compression on the broker level might lead to performance issues. See Compression Handling by Kafka and Kafka-based applications.

## Known caveats
### Filesystem permissions with Java-based clients
Most of these compression algorithms (with the exception of GZIP) require specific permissions, as they are implemented through JNI wrappers on top of native C/C++ libraries which are packaged in the .jar files (.so or .dll, for multiple platforms), and generally extracted in the system’s default temporary folder (/tmp , %USERPROFILE%\AppData\Local\Temp or C:\Windows\Temp), unless overridden. If you are running on a hardened operating system, that folder probably won't have executable permissions (exec on linux, or Read on linux).
For example, if this happens with Snappy, you can either:
* Pass the -Dorg.xerial.snappy.tempdir=(path to new temp which has exec permissions) JVM argument 
* Override the -Djava.io.tmpdir=(path to new temp which has exec permissions) JVM argument 
* remount /tmp with exec permissions: mount -o remount,exec /tmp
### Version mismatch
When using the ZStd compression algorithm, be careful to double-check that all components of your architecture use version 2.1 or greater of Kafka (Confluent Platform 5.1 or greater).
Compression handling by Kafka and Kafka-based applications

Compression can be handled by producers, brokers (topic-based) or both. However, it is generally considered better to configure compression only at the application level, as choosing any of the two other options might induce additional cost if:
* Compression has not been enabled at the producer level, the broker will compress messages based on the algorithm configured for the topic. 
* The algorithm chosen for the broker differs from the algorithm configured for the producer, the broker will decompress and recompress messages to use the algorithm configured for the topic.

* Due to that fact, it is generally considered better to configure compression at the application level.
Compression does not need to be configured at the consumer level, as producers or brokers will provide the compression algorithm to consumers in a message header, meaning that consumers will automatically choose the right algorithm.

