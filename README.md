# SamzaHivePigNeo4J
Building real time data pipelines with Kafka and Samza.

The MyPredictiveFarm datapipline
================================

Meta information store: Graph database either Neo4j or Apache giraph.

Lowest level backend store:

- Neo4j (retina blood vessels model)

How data streams are processed
=========================

Consuming messages from more than one input stream, and all input streams have messages available, messages are processed in a round robin fashion by default. 

MyPredictiveFarm example, if a job is consuming AdFarmingEvent and AdDeviceEvent, the task instance’s process() method is called with a message from AdMessageEvent, then a message from AdDeviceEvent, then another message from AdImpressionEvent,
and continues to alternate between the two.


