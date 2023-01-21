# Consumer Offset Management

A consumer group is a set of consumers which cooperate to consume data from some topics. The partitions of all the topics are divided among the consumers in the group. As new group members arrive and old members leave, the partitions are re-assigned so that each member receives a proportional share of the partitions. This is known as rebalancing the group. After the consumer receives its assignment from the coordinator, it must determine the initial position for each assigned partition. When the group is first created, before any messages have been consumed, the position is set according to a configurable offset reset policy (auto.offset.reset). Typically, consumption starts either at the earliest offset or the latest offset.

As a consumer in the group reads messages from the partitions assigned by the coordinator, it must commit the offsets corresponding to the messages it has read. If the consumer crashes or is shut down, its partitions will be re-assigned to another member, which will begin consumption from the last committed offset of each partition. If the consumer crashes before any offset has been committed, then the consumer which takes over its partitions will use the reset policy.

The offset commit policy is crucial to providing the message delivery guarantees needed by your application. By default, the consumer is configured to use an automatic commit policy, which triggers a commit on a periodic interval. The consumer also supports a commit API which can be used for manual offset management.

By default, the consumer is configured to auto-commit offsets. Using auto-commit gives you “at least once” delivery: Kafka guarantees that no messages will be missed, but duplicates are possible. Auto-commit basically works as a cron with a period set through the auto.commit.interval.ms configuration property. If the consumer crashes, then after a restart or a rebalance, the position of all partitions owned by the crashed consumer will be reset to the last committed offset. When this happens, the last committed position may be as old as the auto-commit interval itself. Any messages which have arrived since the last commit will have to be read again.

Clearly if you want to reduce the window for duplicates, you can reduce the auto-commit interval, but some users may want even finer control over offsets. The consumer therefore supports a commit API which gives you full control over offsets. Note that when you use the commit API directly, you should first disable auto-commit in the configuration by setting the enable.auto.commit property to false. Each call to the commit API results in an offset commit request being sent to the broker. Using the synchronous API, the consumer is blocked until that request returns successfully. This may reduce overall throughput since the consumer might otherwise be able to process records while that commit is pending.

One way to deal with this is to increase the amount of data that is returned when polling. The consumer has a configuration setting fetch.min.bytes which controls how much data is returned in each fetch. The broker will hold on to the fetch until enough data is available (or fetch.max.wait.ms expires). The tradeoff, however, is that this also increases the amount of duplicates that have to be dealt with in a worst-case failure. A second option is to use asynchronous commits. Instead of waiting for the request to complete, the consumer can send the request and return immediately by using asynchronous commits.

## Group Configuration

You should always configure group.id unless you are using the simple assignment API and you don’t need to store offsets in Kafka.

You can control the session timeout by overriding the session.timeout.ms value. The default is 10 seconds in the C/C++ and Java clients, but you can increase the time to avoid excessive rebalancing, for example due to poor network connectivity or long GC pauses.

The main drawback to using a larger session timeout is that it will take longer for the coordinator to detect when a consumer instance has crashed, which means it will also take longer for another consumer in the group to take over its partitions. For normal shutdowns, however, the consumer sends an explicit request to the coordinator to leave the group which triggers an immediate rebalance.

The other setting which affects rebalance behavior is heartbeat.interval.ms. This controls how often the consumer will send heartbeats to the coordinator. It is also the way that the consumer detects when a rebalance is needed, so a lower heartbeat interval will generally mean faster rebalancing. The default setting is three seconds. For larger groups, it may be wise to increase this setting.

Another property that could affect excessive rebalancing is max.poll.interval.ms. This property specifies the maximum time allowed time between calls to the consumers poll method (Consume method in .NET) before the consumer process is assumed to have failed. The default is 300 seconds and can be safely increased if your application requires more time to process messages. If you are using the Java consumer, you can also adjust max.poll.records to tune the number of records that are handled on every loop iteration.

## Offset Management

The two main settings affecting offset management are whether auto-commit is enabled and the offset reset policy. First, if you set enable.auto.commit (which is the default), then the consumer will automatically commit offsets periodically at the interval set by auto.commit.interval.ms. The default is 5 seconds.

Second, use auto.offset.reset to define the behavior of the consumer when there is no committed position (which would be the case when the group is first initialized) or when an offset is out of range. You can choose either to reset the position to the “earliest” offset or the “latest” offset (the default). You can also select “none” if you would rather set the initial offset yourself and you are willing to handle out of range errors manually.


