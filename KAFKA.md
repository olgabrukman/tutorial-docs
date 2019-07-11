[Quick Getting Started](https://kafka.apache.org/quickstart)

## Overview

https://kafka.apache.org/intro

Kafka is generally used for two broad classes of applications:
+ Building real-time streaming data pipelines that reliably get data between systems or applications
+ Building real-time streaming applications that transform or react to the streams of data

Concepts:
+ Kafka is run as a cluster on one or more servers that can span multiple data centers.
+ The Kafka cluster stores streams of records in categories called topics.
+ Each record consists of a key, a value, and a timestamp.

Kafka has four core APIs:
+ The Producer API allows an application to publish a stream of records to one or more Kafka topics.
+ The Consumer API allows an application to subscribe to one or more topics and process the stream of 
records produced to them.
+ The Streams API allows an application to act as a stream processor, consuming an input stream from one 
or more topics and producing an output stream to one or more output topics, effectively transforming the 
nput streams to output streams.
+ The Connector API allows building and running reusable producers or consumers that connect Kafka 
topics to existing applications or data systems. For example, a connector to a relational database might capture every change to a table.

https://sookocheff.com/post/kafka/kafka-in-a-nutshell/
https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster
https://blog.newrelic.com/engineering/effective-strategies-kafka-topic-partitioning/
