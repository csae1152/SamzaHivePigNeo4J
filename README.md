# SamzaHivePigNeo4J
Building real time data pipelines with Kafka and Samza.

The MyPredictiveFarm datapipline
================================

Meta information store: Graph database either Neo4j or Apache giraph.

Lowest level backend store:

- Neo4j retina blood vessel

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

Fault Tolerance and Isolation

Samza provides fault tolerance by restarting containers that fail (potentially on another machine) and resuming processing of the stream. Samza resumes from the same offset by using “checkpoints”. The Samza container periodically checkpoints the current offset for each input stream partition that a task consumes. When the container starts up again after a failure, it looks for the most recent checkpoint and starts consuming messages from the checkpointed offsets. This guarantees at-least-once processing of messages.

One of the advantages of being tightly integrated with Kafka is that a failure in a downstream job does not cause back-pressure on upstream jobs. Normally, when a job fails, the job producing input for the failed job must decide what to do: it has messages that need to be sent, but the downstream job is not available to process them. It can drop the messages, block until downstream processing resumes, or store them locally until the job comes back. Samza avoids this problem by sending all messages between jobs to Kafka. This allows upstream jobs to continue processing at full speed without worrying about losing its output, even in scenarios where the jobs that are processing might be down. Messages are stored on Kafka brokers even when a job is unavailable.

By using independent tasks which process different partitions of the input streams and isolating the task execution by means of containers, Samza achieves process isolation and fault tolerance.  Since each container is a separate UNIX process, the execution framework (that Samza integrates with) can easily migrate a process from one machine to another, incase any of the containers starts hogging the resources of a machine. Process isolation also means that when one job fails, it does not impact other jobs in the cluster. There are some caveats to this, which are documented here.

Stateful Stream Processing

One of the unique features that sets Samza apart from other stream processing systems is its built-in support for stateful stream processing. Some stream processing tasks are stateless and operate on one record at a time, but other uses such as counts, aggregation or joins over a window in the stream require state to be buffered in the system.

NLP and advanced stream processing

Working with Data
Pig allows you to transform data in many ways. As a starting point, become familiar with these operators:

Use the FILTER operator to work with tuples or rows of data. Use the FOREACH operator to work with columns of data.
Use the GROUP operator to group data in a single relation. Use the COGROUP, inner JOIN, and outer JOIN operators to group or join data in two or more relations.
Use the UNION operator to merge the contents of two or more relations. Use the SPLIT operator to partition the contents of a relation into multiple relations.

Execution Framework

One final decision we made was to not build a custom distributed execution system in Samza. Instead, execution is pluggable, and currently completely handled by YARN. This has two benefits.

The first benefit is practical: there is another team of smart people working on the execution framework. YARN is developing at a rapid pace, and already supports a rich set of features around resource quotas and security. This allows you to control what portion of the cluster is allocated to which users and groups, and also control the resource utilization on individual nodes (CPU, memory, etc) via cgroups. YARN is run at massive scale to support Hadoop and will likely become an ubiquitous layer. Since Samza runs entirely through YARN, there are no separate daemons or masters to run beyond the YARN cluster itself. In other words, if you already have Kafka and YARN, you don’t need to install anything in order to run Samza jobs.

Secondly, our integration with YARN is completely componentized. It exists in a separate package, and the main Samza framework does not depend on it at build time. This means that YARN can be replaced with other virtualization frameworks — in particular, we are interested in adding direct AWS integration. Many companies run in AWS which is itself a virtualization framework, which for Samza’s purposes is equivalent to YARN: it allows you to create and destroy virtual “container” machines and guarantees fixed resources for these containers. Since stream processing jobs are long-running, it is a bit silly to run a YARN cluster inside AWS and then schedule individual jobs within this cluster. Instead, a more sensible approach would be to directly allocate a set of EC2 instances for your jobs.

We think there will be a lot of innovation both in open source virtualization frameworks like Mesos and YARN and in commercial cloud providers like Amazon, so it makes sense to integrate with them.

Combining Samza and Spring boot.

Turning the database inside out.

Use Local Data to Run Hello Samza

Containers

Partitions and tasks are both logical units of parallelism—they don’t correspond to any particular assignment of computational resources (CPU, memory, disk space, etc). Containers are the unit of physical parallelism, and a container is essentially a Unix process (or Linux cgroup). Each container runs one or more tasks. The number of tasks is determined automatically from the number of partitions in the input and is fixed, but the number of containers (and the CPU and memory resources associated with them) is specified by the user at run 

Let as have a look at Partitions

WHAT IS PIG?

Pig is a high level scripting language that is used with Apache Hadoop. Pig excels at describing data analysis problems as data flows. Pig is complete in that you can do all the required data manipulations in Apache Hadoop with Pig. In addition through the User Defined Functions(UDF) facility in Pig you can have Pig invoke code in many languages like JRuby, Jython and Java. Conversely you can execute Pig scripts in other languages. The result is that you can use Pig as a component to build larger and more complex applications that tackle real business problems.

A good example of a Pig application is the ETL transaction model that describes how a process will extract data from a source, transform it according to a rule set and then load it into a datastore. Pig can ingest data from files, streams or other sources using the User Defined Functions(UDF). Once it has the data it can perform select, iteration, and other transforms over the data. Again the UDF feature allows passing the data to more complex algorithms for the transform. Finally Pig can store the results into the Hadoop Data File System.

What Is Hive

Hive is a data warehousing infrastructure based on Apache Hadoop. Hadoop provides massive scale out and fault tolerance capabilities for data storage and processing on commodity hardware.
Hive is designed to enable easy data summarization, ad-hoc querying and analysis of large volumes of data. It provides SQL which enables users to do ad-hoc querying, summarization and data analysis easily. At the same time, Hive's SQL gives users multiple places to integrate their own functionality to do custom analysis, such as User Defined Functions (UDFs).  

What are Data Units

In the order of granularity - Hive data is organized into:
Databases: Namespaces function to avoid naming conflicts for tables, views, partitions, columns, and so on.  Databases can also be used to enforce security for a user or group of users.

Tables: Homogeneous units of data which have the same schema. An example of a table could be page_views table, where each row could comprise of the following columns (schema):

timestamp—which is of INT type that corresponds to a UNIX timestamp of when the page was viewed.
userid —which is of BIGINT type that identifies the user who viewed the page.
page_url—which is of STRING type that captures the location of the page.
referer_url—which is of STRING that captures the location of the page from where the user arrived at the current page.
IP—which is of STRING type that captures the IP address from where the page request was made.
Partitions: Each Table can have one or more partition Keys which determines how the data is stored. Partitions—apart from being storage units—also allow the user to efficiently identify the rows that satisfy a specified criteria; for example, a date_partition of type STRING and country_partition of type STRING. Each unique value of the partition keys defines a partition of the Table. For example, all "US" data from "2009-12-23" is a partition of the page_views table. Therefore, if you run analysis on only the "US" data for 2009-12-23, you can run that query only on the relevant partition of the table, thereby speeding up the analysis significantly. Note however, that just because a partition is named 2009-12-23 does not mean that it contains all or only data from that date; partitions are named after dates for convenience; it is the user's job to guarantee the relationship between partition name and data content! Partition columns are virtual columns, they are not part of the data itself but are derived on load.

Buckets (or Clusters): Data in each partition may in turn be divided into Buckets based on the value of a hash function of some column of the Table. For example the page_views table may be bucketed by userid, which is one of the columns, other than the partitions columns, of the page_view table. These can be used to efficiently sample the data.

Building a data pipeline.

As a data sink we have our app.

Connected through Kafka with Spark.

For storing our logs we use Cassandra.

Cassandra and Astyanax.

The inside-out database

For the most part, our backend engineering team is in the business of building indexes that support fast aggregates on top of event streams. In our data stack, the indexes live in Druid, but the story begins with the original event streams. These are generally standard ad-tech data types like bid requests, bid responses, impressions, and clicks.

Our first attempt at a streaming data stack lacked a data integration component. We tried to just use Druid to directly index incoming data from our customers. There was a serious mismatch here: even though Druid can index streams, it can only do that in a very particular way. It needs a single stream with dimensions and metrics cleanly separated. We didn’t have that: we had multiple streams with messy data. We needed to join the streams (so we could find out whether ads were served and clicked on) and we needed to transform them a bit. For a while, this mismatch prevented us from actually being able to index data in real-time. At first we tried to get our users to fix up their data for us, and send us data that was already well-suited for Druid indexing, but that flopped. People generally expected us to do the work on our end.













