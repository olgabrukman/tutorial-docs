## NOSQL: General Info

https://en.wikipedia.org/wiki/NoSQL

Motivations for this approach include: simplicity of design, simpler "horizontal" scaling 
to clusters of machines (which is a problem for relational databases), and finer control 
over availability. The data structures used by NoSQL databases (e.g. key-value, wide column, 
graph, or document) are different from those used by default in relational databases, making 
some operations faster in NoSQL. The particular suitability of a given NoSQL database depends 
on the problem it must solve. Sometimes the data structures used by NoSQL databases are also 
viewed as "more flexible" than relational database tables.

Many NoSQL stores compromise consistency (in the sense of the CAP theorem) in favor of availability, 
partition tolerance, and speed. Barriers to the greater adoption of NoSQL stores include the use of 
low-level query languages (instead of SQL, for instance the lack of ability to perform ad-hoc joins 
across tables), lack of standardized interfaces, and huge previous investments in existing relational 
databases.

Instead, most NoSQL databases offer a concept of "eventual consistency" in which database changes 
are propagated to all nodes "eventually" (typically within milliseconds) so queries for data might 
not return updated data immediately or might result in reading data that is not accurate, a problem 
known as stale reads. Additionally, some NoSQL systems may exhibit lost writes and other forms 
of data loss.

## NoSQL DB Types
* **Column**: <Unique name, Value, Timestamp>
* **Key-Value**: dictionary/hash table of <key, value object>
* **Document**: sub-type of Key-Value DB, <key, document object, document metadata>
* **Graph**: Graph databases, by design, allow simple and fast retrieval of complex hierarchical structures 
that are difficult to model in relational systems. Graph databases are similar to 1970s network model 
databases in that both represent general graphs, but network-model databases operate at a lower level 
of abstraction and lack easy traversal over a chain of edges.

## Most Popular NoSQL DBs
https://www.infoworld.com/article/2848722/nosql/mongodb-cassandra-hbase-three-nosql-databases-to-watch.html

### MongoDB
MongoDB has aimed for a balanced approach suited to a wide variety of applications. 
MongoDB rightly gets credit for an easy out-of-the-box experience.
While the functionality is close to that of a traditional relational database, 
MongoDB allows users to capitalize on the benefits of cloud infrastructure with 
its horizontal scalability and to easily work with the diverse data sets in use 
today thanks to its flexible data model.
MongoDB is designed for OLTP (Online transaction processing) workloads. It can do complex queries, but it’s not 
necessarily the best fit for reporting-style workloads. Or if you need complex 
transactions, it’s not going to be a good choice. 

### Cassandra
Cassandra rightly gets credit for operational simplicity, i.e., easy to manage at scale.
Users tend to gravitate to Cassandra the more they butt their heads against the difficulty 
of making relational databases faster and more reliable, particularly at scale.


Cassandra is essentially a hybrid between a key-value and a column-oriented (or tabular) database. Its data model is 
a partitioned row store with tunable consistency. Rows are organized into tables; the first component of 
a table's primary key is the partition key; within a partition, rows are clustered by the remaining columns 
of the key. Other columns may be indexed separately from the primary key.

Tables may be created, dropped, and altered at run-time without blocking updates and queries.
Cassandra does not support joins or subqueries. Rather, Cassandra emphasizes denormalization 
through features like collections. A column family (called "table" since CQL 3) resembles a table 
in an RDBMS. Column families contain rows and columns. Each row is uniquely identified by a row key. 
Each row has multiple columns, each of which has a name, value, and a timestamp. Unlike a table in 
an RDBMS, different rows in the same column family do not have to share the same set of columns, 
and a column may be added to one or multiple rows at any time. Each key in Cassandra corresponds 
to a value which is an object. Each key has values as columns, and columns are grouped together 
into sets called column families. Thus, each key identifies a row of a variable number of elements. 
These column families could be considered then as tables. A table in Cassandra is a distributed 
multi dimensional map indexed by a key. Furthermore, applications can specify the sort order of 
columns within a Super Column or Simple Column family.

Why switch from relational DB to to NoSQL DB? Cassandra can handle huge volumes of writes.
Fast writes - Cassandra was designed to run on cheap commodity hardware. It performs blazingly 
fast writes and can store hundreds of terabytes of data, without sacrificing the read efficiency.

### HBase
HBase provides a record-based storage layer that enables fast, random reads and writes to data, 
complementing Hadoop by emphasizing high throughput at the expense of low-latency I/O.

Because it can utilize the storage, memory, and CPU resources of any number of servers, 
as well as has scale-out features like automatic sharding, HBase can scale limitlessly 
as load and performance demands increase simply by adding server nodes. HBase was 
designed from the ground up to provide optimal performance when consistency is critical.

Thanks to its tight integration with the rest of the Hadoop ecosystem, data is readily 
available to users and applications via SQL queries (using Cloudera Impala, Apache Phoenix, 
or Apache Hive) or even faceted free-text search (using Cloudera Search).” Thus, HBase 
gives developers a way to leverage existing expertise with SQL while building on 
a more modern, distributed database.
