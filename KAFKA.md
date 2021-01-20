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

### Consumer groups
Multiple consumer groups can read from the same set of topics, and at different times catering to different logical application domains. Thus, Kafka provides both the advantage of high scalability via consumers belonging to the same consumer group and the ability to serve multiple independent downstream applications simultaneously.  

If all consumers are from the same group, the Kafka model functions as a traditional message queue would. All the records and processing is then load balanced  Each message would be consumed by one consumer of the group only. Each partition is connected to at most one consumer from a group.

When multiple consumer groups exist, the flow of the data consumption model aligns with the traditional publish-subscribe model. The messages are broadcast to all consumer groups.

Ideally, the number of partitions is equal to the number of consumers. Should the number of consumers be greater, the excess consumers are idle, wasting client resources. If the number of partitions is greater, some consumers will read from multiple partitions which should not be an issue unless the ordering of messages is important to the use case. Kafka does not guarantee ordering of messages between partitions. It does provide ordering within a partition. Thus, Kafka can maintain message order by a consumer if it is subscribed to only a single partition. Messages can also be ordered using the key to be grouped by during processing.

Kafka also eliminates issues around the reliability of message delivery by having the option of acknowledgments in the form or offset commits of delivery sent to the broker to ensure it has reached the subscribed groups. As partitions can only have a one to one or many to one relationship to consumers in a consumer group, the replication of a message within a consumer group is avoided as a given message is reaching only one consumer in the group at a time.

https://blog.cloudera.com/scalability-of-kafka-messaging-using-consumer-groups/#:~:text=Kafka%20consumers%20belonging%20to%20the,single%20consumer%20from%20the%20group.

#### Kafka At-Most-Once
Read the message and save its offset position before it possibly processes the message record in entirety; i.e. save to data lake. In case of failure, the consumer recovery process will resume from the previously saved offset position which is further beyond the last record saved to the data lake. This example demonstrates at-most-once semantics.  Data loss at data lake, for example, is possible.

#### Kafka At-Least-Once
The consumer can also read, process the messages and save to data lake, and afterward save its offset position.  In a failure scenario which occurs after the data lake storage and offset location update, a Kafka consumer addressing the failed process could duplicate data. This forms the “at-least-once” semantics in case of consumer failure.  Duplicates in the data lake could happen.

#### Kafka Exactly Once
When it comes to exactly once semantics with Kafka and external systems, the restriction is not necessarily a feature of the messaging system, but the necessity for coordination of the consumer’s position with what is stored in the destination.  For example, a destination might be in an HDFS or object store based data lake. One of the most common ways to do this is by introducing a two-phase commit between the consumer output’s storage and the consumer position’s storage.  Or in other words, taking control of offset location commits in relation to data lake storage on a per record basis.  

https://sookocheff.com/post/kafka/kafka-in-a-nutshell/
https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster
https://blog.newrelic.com/engineering/effective-strategies-kafka-topic-partitioning/


