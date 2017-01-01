# SamzaHivePigNeo4J
Building real time data pipelines with Kafka and Samza.

The MyPredictiveFarm datapipline
================================

Meta information store: Graph database either Neo4j or Apache giraph.

Lowest level backend store:

- Neo4j (retina blood vessel

We can access our data model via a rest api.

We will use Spring data as a wrapper for accessing neo4j via cipher.

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

Differences between Spark, Storm and Samza:

Spark uses ZeroMQ as an underlaying messaging service.

Spark vs. Samza
==========================

Samza is lower level. 
Samza uses per-message semantics
Windowing for microbatch action

Spark uses inherent topological processing graph.

What are inherent topological processing graphs.

Combining Samza with Kafka for aggregating the retina images and collecting meta information.

Apache Spark is a clustered, in-memory data processing solution that scales processing of large datasets easily across many machines. It also comes with GraphX and GraphFrames two frameworks for running graph compute operations on your data.

You can integrate with Spark in a variety of ways. Either to pre-process (aggregate, filter, convert) your raw data to be imported into Neo4j.

Spark can also serve as external Graph Compute solution, where you

export data of selected subgraphs from Neo4j to Spark,
compute the analytic aspects, and
write the results back to Neo4j
to be used in your Neo4j operations and Cypher queries.

Cypher explained:
=================

Cypher is a SQL like query language for traversing through a graph.

Seeting up an online neo4J database with GrapheneDB.

The Hadoop Distributed File System (HDFS) offers a way to store large files across multiple machines. Hadoop and HDFS was derived from Google File System (GFS) paper. Prior to Hadoop 2.0.0, the NameNode was a single point of failure (SPOF) in an HDFS cluster. With Zookeeper the HDFS High Availability feature addresses this problem by providing the option of running two redundant NameNodes in the same cluster in an Active/Passive configuration with a hot standby.

Cypher and Spring data.

Spring Boot and Spring data.

Spring boot bug while working with Spring data.

This is a well know bug working with Spring boot's auto configuration.

Spring Boot is not able to find bean's annotated with @Bean

There are workarounds...

Using Spring Social Twitter to grap twitter images from a samza stream.

Examples:

OutgoingMessageEnvelope
public OutgoingMessageEnvelope(SystemStream systemStream,
                       java.lang.Object key,
                       java.lang.Object message)
Constructs a new OutgoingMessageEnvelope from specified components.
Parameters:
systemStream - Object representing the appropriate stream of which this envelope will be sent on.
key - A deserialized key to be used for the message.
message - A deserialized message to be sent in this envelope.

Build Your Own Spring Boot Starter – Stormpath and Samza

We started building Spring Boot starters for our own product. A few months ago, we launched the Stormpath Spring Boot Starter, with the idea that we would soon use it in other applications we wrote for our own needs. It handles a lot of user management features, and we eat our own dog food.

More recently, we developed a Spring Boot Starter for Apache Samza, which we open-sourced under the Apache 2.0 license. Samza is a real-time streaming product that traditionally requires YARN, a complex infrastructure to launch JVMs and manage memory across them. I was frustrated by how complicated it is to deploy Samza, so we reverse engineered the parts that launch a Samza container, and turned it into a Spring Boot Starter.

Now, anytime we have a Samza microservice that consumes Kafka events, we can skip the complex deployment process (unzipping a tarball, running a shell script to launch the process and connecting to a YARN environment, etc.). It’s now just a simple Spring Boot application that can be launched from the command line, and it changed our entire microservices architecture in a week’s worth of work.

State Management

One of the more interesting features of Samza is stateful stream processing. Tasks can store and query data through APIs provided by Samza. That data is stored on the same machine as the stream task; compared to connecting over the network to a remote database, Samza’s local state allows you to read and write large amounts of data with better performance. Samza replicates this state across multiple machines for fault-tolerance (described in detail below).

Creating a Spring boot application with a Neo4j database
========================================================

We will use GrapheneDB for hosting our Neo4J instances.












