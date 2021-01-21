## Kafka Overview
[Kafka](https://kafka.apache.org/intro) is essentially a commit log with a very simplistic data structure. It just so happens to be exceptionally fault-tolerant, horizontally scalable, and capable of handling huge throughput. This has made Kafka extremely popular for many large enterprise organisations, where applications range from pub-sub messaging to log aggregation.

*Kafka is generally used for two broad classes of applications:*
+ Building real-time streaming data pipelines that reliably get data between systems or applications
+ Building real-time streaming applications that transform or react to the streams of data

Kafka is run as a cluster on one or more servers that can span multiple data centers.
The Kafka cluster stores streams of records in categories called topics.
Each record consists of a key, a value, and a timestamp.

*Kafka has four core APIs:*
+ The Producer API allows an application to publish a stream of records to one or more Kafka topics.
+ The Consumer API allows an application to subscribe to one or more topics and process the stream of 
records produced to them.
+ The Streams API allows an application to act as a stream processor, consuming an input stream from one 
or more topics and producing an output stream to one or more output topics, effectively transforming the 
nput streams to output streams.
+ The Connector API allows building and running reusable producers or consumers that connect Kafka 
topics to existing applications or data systems. For example, a connector to a relational database might capture every change to a table.

### Key Concepts
[Reference](https://blog.cloudera.com/scalability-of-kafka-messaging-using-consumer-groups/#:~:text=Kafka%20consumers%20belonging%20to%20the,single%20consumer%20from%20the%20group)
[Visual tutorial to Kafka](https://www.slideshare.net/PaulBrebner/a-visual-introduction-to-apache-kafka)
+ Kafka cluster: A distributed system of Kafka brokers.
+ Kafka broker: The message broker responsible for mediating the data between the producers and the consumers. They’re responsible for the bulk of I/O operations and durable persistence within the cluster.
+ ZooKeeper: Manages the overall controller status in the cluster. It acts as a configuration repository, maintaining cluster metadata and also implementing the actual mechanics of the cluster.
+ Kafka producer: Client applications responsible for appending records to Kafka topics.
+ Kafka consumer: Client applications that read from topics.
+ Topic: messages are published to a topic, consumers are subscribed to a topic(s).
+ Partition: Ideally, the number of partitions is equal to the number of consumers. Should the number of consumers be greater, the excess consumers are idle, wasting client resources. If the number of partitions is greater, some consumers will read from multiple partitions which should not be an issue unless the ordering of messages is important to the use case. Kafka does not guarantee ordering of messages between partitions. It does provide ordering within a partition. Thus, Kafka can maintain message order by a consumer if it is subscribed to only a single partition. Messages can also be ordered using the key to be grouped by during processing.
+ Consumer group: Multiple consumer groups can read from the same set of topics, and at different times catering to different logical application domains. Thus, Kafka provides both the advantage of high scalability via consumers belonging to the same consumer group and the ability to serve multiple independent downstream applications simultaneously. If all consumers are from the same group, the Kafka model functions as a traditional message queue would. All the records and processing is then load balanced  Each message would be consumed by one consumer of the group only. Each partition is connected to at most one consumer from a group. When multiple consumer groups exist, the flow of the data consumption model aligns with the traditional publish-subscribe model. The messages are broadcast to all consumer groups.
  

### Delivery Modes
Kafka also eliminates issues around the reliability of message delivery by having the option of acknowledgments in the form or offset commits of delivery sent to the broker to ensure it has reached the subscribed groups. As partitions can only have a one to one or many to one relationship to consumers in a consumer group, the replication of a message within a consumer group is avoided as a given message is reaching only one consumer in the group at a time.

#### Kafka At-Most-Once
Read the message and save its offset position before it possibly processes the message record in entirety; i.e. save to data lake. In case of failure, the consumer recovery process will resume from the previously saved offset position which is further beyond the last record saved to the data lake. This example demonstrates at-most-once semantics.  Data loss at data lake, for example, is possible.

#### Kafka At-Least-Once
The consumer can also read, process the messages and save to data lake, and afterward save its offset position.  In a failure scenario which occurs after the data lake storage and offset location update, a Kafka consumer addressing the failed process could duplicate data. This forms the “at-least-once” semantics in case of consumer failure.  Duplicates in the data lake could happen.

#### Kafka Exactly Once
When it comes to exactly once semantics with Kafka and external systems, the restriction is not necessarily a feature of the messaging system, but the necessity for coordination of the consumer’s position with what is stored in the destination.  For example, a destination might be in an HDFS or object store based data lake. One of the most common ways to do this is by introducing a two-phase commit between the consumer output’s storage and the consumer position’s storage.  Or in other words, taking control of offset location commits in relation to data lake storage on a per record basis.  

https://sookocheff.com/post/kafka/kafka-in-a-nutshell/
https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster
https://blog.newrelic.com/engineering/effective-strategies-kafka-topic-partitioning/

## Hands-on: Run Kafka Cluster, Run Kafdrop, And Send/Read Messages
+ [Start simple kafka cluster in docker containers](https://medium.com/better-programming/a-simple-apache-kafka-cluster-with-docker-kafdrop-and-python-cf45ab99e2b9)

```
;docker-compose.yml
version: '3'
services:
  zookeeper:
    image: zookeeper:3.4.9
    hostname: zookeeper
    ports:
      - "2181:2181"
    environment:
      ZOO_MY_ID: 1
      ZOO_PORT: 2181
      ZOO_SERVERS: server.1=zookeeper:2888:3888
    volumes:
      - ./data/zookeeper/data:/data
      - ./data/zookeeper/datalog:/datalog
  
  kafka1:
    image: confluentinc/cp-kafka:5.3.0
    hostname: kafka1
    ports:
      - "9091:9091"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka1:19091,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9091
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zookeeper:2181"
      KAFKA_BROKER_ID: 1
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - ./data/kafka1/data:/var/lib/kafka/data
    depends_on:
      - zookeeper
  kafka2:
    image: confluentinc/cp-kafka:5.3.0
    hostname: kafka2
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka2:19092,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_BROKER_ID: 2
    volumes:
      - ./data/kafka2/data:/var/lib/kafka/data
    depends_on:
      - zookeeper 
  kafka3:
    image: confluentinc/cp-kafka:5.3.0
    hostname: kafka3
    ports:
      - "9093:9093"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka3:19093,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zookeeper:2181"
      KAFKA_BROKER_ID: 3
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - ./data/kafka3/data:/var/lib/kafka/data
    depends_on:
      - zookeeper  
  kafdrop:
    image: obsidiandynamics/kafdrop
    restart: "no"
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka1:19091"
    depends_on:
      - kafka1
      - kafka2
      - kafka3     
```
Monitor kafka topics, zookeeper, consumers by accessing kafdrop instance (localhost:9000).

Listen and publish messages to kafka:
```
kafkacat -C -b localhost:9091,localhost:9092,localhost:9093 -t my-topic -p 0

echo 'publish to partition 0' | kafkacat -P -b localhost:9091,localhost:9092,localhost:9093 -t my-topic -p 0
```
+ [Start Kafka locally](https://kafka.apache.org/quickstart)

## Kafka vs Messaging Queues (RabbitMQ, NAT, Pub/Sub)
![alt text](https://www.upsolver.com/wp-content/uploads/2019/05/Screen-Shot-2020-05-25-at-16.13.53.png)
Apache Kafka and RabbitMQ are two open-source and commercially-supported pub/sub systems, readily adopted by enterprises. RabbitMQ is an older tool released in 2007 and was a primary component in messaging and SOA systems. Today it is also being used for streaming use cases. Kafka is a newer tool, released in 2011, which, from the onset, was built for streaming scenarios.

RabbitMQ is a general purpose message broker that supports protocols including, MQTT, AMQP, and STOMP. It can deal with high-throughput use cases, such as online payment processing. It can handle background jobs or act as a message broker between microservices.

Kafka is a message bus developed for high-ingress data replay and streams. Kafka is a durable message broker that enables applications to process, persist, and re-process streamed data. Kafka has a straightforward routing approach that uses a routing key to send messages to a topic.

RabbitMQ Architecture
General-purpose message broker—uses variations of request/reply, point to point, and pub-sub communication patterns.
Smart broker / dumb consumer model—consistent delivery of messages to consumers, at around the same speed as the broker monitors the consumer state.
Mature platform—well supported, available for Java, client libraries, .NET, Ruby, node.js. Offers dozens of plugins.
Communication—can be synchronous or asynchronous.
Deployment scenarios—provides distributed deployment scenarios.
Multi-node cluster to cluster federation—does not rely on external services, however, specific cluster formation plugins can use DNS, APIs, Consul, etc.


Apache Kafka Architecture
High volume publish-subscribe messages and streams platform—durable, fast, and scalable.
Durable message store—like a log, run in a server cluster, which keeps streams of records in topics (categories).
Messages—made up of a value, a key and a timestamp.
Dumb broker / smart consumer model—does not try to track which messages are read by consumers and only keeps unread messages. Kafka keeps all messages for a set period of time.
Requires external services to run—in some cases Apache Zookeeper.

Apache Kafka: Pull-based approach
Kafka uses a pull model. Consumers request batches of messages from a specific offset. Kafka permits long-pooling, which prevents tight loops when there is no message past the offset.

A pull model is logical for Kafka because of its partitions. Kafka provides message order in a partition with no contending consumers. This allows users to leverage the batching of messages for effective message delivery and higher throughput.

RabbitMQ: Push-based approach
RabbitMQ uses a push model and stops overwhelming consumers through a prefetch limit defined on the consumer. This can be used for low latency messaging.

The aim of the push model is to distribute messages individually and quickly, to ensure that work is parallelized evenly and that messages are processed approximately in the order in which they arrived in the queue.

Kafka vs RabbitMQ Performance
Apache Kafka:
Kafka offers much higher performance than message brokers like RabbitMQ. It uses sequential disk I/O to boost performance, making it a suitable option for implementing queues. It can achieve high throughput (millions of messages per second) with limited resources, a necessity for big data use cases.

RabbitMQ:
RabbitMQ can also process a million messages per second but requires more resources (around 30 nodes). You can use RabbitMQ for many of the same use cases as Kafka, but you’ll need to combine it with other tools like Apache Cassandra.

Kafka:
Streams with complex routing, the throughput of 100K/sec events or more, with “at least once” partitioned ordering
Applications requiring a stream history, delivered in “at least once” partitioned ordering. Clients can see a ‘replay’ of the event stream.
Event sourcing, modeling changes to a system as a sequence of events.
Stream processing data in multi-stage pipelines. The pipelines generate graphs of real-time data flows.

RabbitMQ:
Granular control over consistency/set of guarantees on a per-message basis
Complex routing to consumers
Applications that need a variety of publish/subscribe, point-to-point request/reply messaging capabilities.

Additional Info:
+ [NAT vs RMQ vs Kafka](https://medium.com/@philipfeng/modern-open-source-messaging-apache-kafka-rabbitmq-nats-pulsar-and-nsq-ca3bf7422db5)
+ [Google Pub/Sub vs Kafka](https://stackoverflow.com/questions/38572071/i-am-evaluating-google-pub-sub-vs-kafka#:~:text=With%20Google%20Pub%2FSub%2C%20once,subscription%20and%20ACKed%2C%20it's%20gone.&text=Amazon%20AWS%20Kinesis%20is%20a,and%20SQS%20provides%20the%20queueing))

