# SamzaHivePigNeo4J
Building real time data pipelines with Kafka and Samza.

The MyPredictiveFarm datapipline
================================

Meta information store: Graph database either Neo4j or Apache giraph.

Lowest level backend store:

- Neo4j (retina blood vessels model)

How data streams are processed
==============================

Consuming messages from more than one input stream, and all input streams have messages available, messages are processed in a round robin fashion by default. 

MyPredictiveFarm example, if a job is consuming AdFarmingEvent and AdDeviceEvent, the task instance’s process() method is called with a message from AdMessageEvent, then a message from AdDeviceEvent, then another message from AdImpressionEvent,
and continues to alternate between the two.

Kafka Apache OSS, mainly from LinkedIn Handles all the logs/event streams High-throughput: millions events/sec High-volume: TBs - PBs of events Low-latency: single-digit msec from producer to consumer Scalable: topics are partitioned across cluster Durable: topics are replicated across cluster Available: auto failover.

Explaining auto failover.

It's a routine for checking if clusters are responding. Which is really important for monitoring your system health.

Samza's key features include:

Simple API: Unlike most low-level messaging system APIs, Samza provides a very simple callback-based process message API comparable to MapReduce.
Managed state: Samza manages snapshotting and restoration of a stream processor's state. When the processor is restarted, Samza restores its state to a consistent snapshot. Samza is built to handle large amounts of state (many gigabytes per partition).
Fault tolerance: Whenever a machine in the cluster fails, Samza works with YARN to transparently migrate your tasks to another machine.
Durability: Samza uses Kafka to guarantee that messages are processed in the order they were written to a partition, and that no messages are ever lost.
Scalability: Samza is partitioned and distributed at every level. Kafka provides ordered, partitioned, replayable, fault-tolerant streams. YARN provides a distributed environment for Samza containers to run in.
Pluggable: Though Samza works out of the box with Kafka and YARN, Samza provides a pluggable API that lets you run Samza with other messaging systems and execution environments.
Processor isolation: Samza works with Apache YARN, which supports Hadoop's security model, and resource isolation through Linux CGroups.

Samza ’s approach to streaming is to process messages as they are received, one at a time. Samza’s stream primitive is not a tuple or a Dstream, but a message. Streams are divided into partitions and each partition is an ordered sequence of read-only messages with each message having a unique ID (offset). The system also supports batching, i.e. consuming several messages from the same stream partition in sequence. Samza`s Execution & Streaming modules are both pluggable, although Samza typically relies on Hadoop’s YARN (Yet Another Resource Negotiator) and Apache Kafka.

Most event processing applications need to either read data from a remote database or need to maintain internal state to produce results. In our experience, the primary bottleneck in these applications is either the I/O and or CPU spent in performing the I/O.

Buffering & Latency

Storm uses ZeroMQ for non-durable communication between bolts, which enables extremely low latency transmission of tuples. Samza does not have an equivalent mechanism, and always writes task output to a stream.

ZeroMQ, ActiveMQ, RabbitMQ
==========================


