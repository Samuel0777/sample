# Overview

Below are configuration that need to be enabled in clients for better performance results

## Throughput: Move as much data as fast as possible

Simply increase partition count?
- Leverage partitions to increase throughput via 
  - Parallel writes 
  - Spreading load over brokers 
  - Scaling number of consumers

Drawbacks
- More file handles per server
- Higher latency
- Higher memory requirements in clients Details
- [How to choose the number of topics/partitions in a Kafka cluster?](https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster/)

###  Producer
- batch.size=250000 (default 16384)
- linger.ms=100 (default 0)
- compression.type=lz4 (default none)
- acks=1 (at expense of durability)
- buffer.memory depending on number of partitions
### Consumer
- fetch.min.bytes=1000000 increases data per fetch from broker (default 1)

## Latency : Move data within the smallest possible time

### Producer
- linger.ms=5 (default 0)
- compression.type=lz4 (default none)
- acks=1 (at expense of durability and possibly not worth it)

### Consumer
- fetch.min.bytes=1 (default 1)

> **_NOTE:_** Producer latency is not the same as E2E latency.[Producer latency is not the same as E2E latency](https://www.confluent.io/blog/configure-kafka-to-minimize-latency/)
 
## Durability: Make sure data is not lost

### Producer
- replication.factor=3 potentially more depending on failure domain
- acks=all
- enable.idempotence=true
to avoid duplication due to retries (default false)
### Consumer
- enable.auto.commit=false to allow manual commits (default true)
 
## Availability: Keep handling reads and writes even under failure scenarios

### Consumer
- session.timeout.ms (affects availability - do not change unless you have very good reasons)
- heartbeat.interval.ms in general 1⁄3 of session.timeout.ms 
- Trade off 
  - Premature eviction from consumer group 
  - Delay to detect a failure



