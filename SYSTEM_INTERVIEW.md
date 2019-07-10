
[SYSTEM DESIGN AND ARCHITECTURE PRIMER: VARIOUS TOPICS](https://github.com/checkcheckzz/system-design-interview)


## System interview numbers 

https://everythingisdata.wordpress.com/2009/10/17/numbers-everyone-should-know/

Operation			 |		Time (nsec)
---------------------------------|----------------------------
L1 cache reference		 |	0.5
Branch mispredict		 |	5
L2 cache reference		 |	7
Mutex lock/unlock		 |	25
Main memory reference		 |    100
Compress 1KB bytes with Zippy	 |	3,000
Send 2K bytes over 1 Gbps network |	20,000
Read 1MB sequentially from memory |	250,000
Roundtrip within same datacenter  |	500,000/(newer 500-1000) 
Disk seek	                  |    10,000,000
Read 1MB sequentially from disk	  |	20,000,000
Send packet CA -> Netherlands -> CA|	150,000,000
System call overhead		   |		400 (newer 60)
Context switch between processes   |	30000
fork() (statically-linked binary)  |	70,000
fork() (dynamically-linked binary) |	160,000


## Scalability for Dummies (4 parts)

http://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones
http://www.lecloud.net/post/7994751381/scalability-for-dummies-part-2-database
http://www.lecloud.net/post/9246290032/scalability-for-dummies-part-3-cache
http://www.lecloud.net/post/9699762917/scalability-for-dummies-part-4-asynchronism

### Horizontal Scaling
Bunch of servers are hidden behind a load balancer. Every server contains exactly the same codebase and does not 
store any user-related data, like sessions or profile pictures, on local disc or memory to avoid being dependant 
on a session state to serve a request. Sessions need to be stored in a centralized data store which is accessible 
to all your application servers, i.e. DB or persistent cache (e.g. Redis).

Horizontal scaling has a bottleneck - relational DB used in the sustem to store sessions and other user data.
* **Path #1: optimize relational DB** do master-slave replication, make master server super strong (add lots of RAM).
	* Sharding is a type of database partitioning that separates very large databases the into smaller, faster, 
	more easily managed parts called data shards
	* Denormalization is a strategy used on a previously-normalized database to increase performance. 
	In computing, denormalization is the process of trying to improve the read performance of a database, 
	at the expense of losing some write performance, by adding redundant copies of data or by grouping data.

* **Path #2: denormalize right from the beginning and include no more Joins in any database query => switch to NoSql DB.**
Joins will now need to be done in your application code. But even if you successfully switch to the latest and greatest 
NoSQL database and let your app do the dataset-joins, soon your database requests will again be slower and slower. You 
will need to introduce a cache.

### Always Use In-Memory Caches
Always use in-memory caches like Memcached or Redis. Never do file-based caching, it makes cloning and auto-scaling of your servers just a pain. A cache is a simple key-value store and it should reside as a buffering layer between your application and your data storage.

There are two patterns of caching your data:
* **Old Pattern: Cached Database Queries.** The main issue is the expiration. When one piece of data changes (for example a table cell) you need to delete all cached queries who may include that table cell. 
*  **Cached objects: Strongly recommended.** See your data as a collection of objects, retrieve them and store them in the cache. Some ideas of objects to cache:
	* user sessions (never use the database!)
	* fully rendered blog articles
	* activity streams
	* user<->friend relationships 

### Asynchronism
Use asynchronious communication instead of busy-wait or even notification-based wait.


## System Interview: How to Design Large-Scale System Step-by-Step

https://github.com/donnemartin/system-design-primer

### Why Is Scalability So Hard? 
Because scalability cannot be an after-thought. It requires applications and platforms to be designed with scaling in mind, 
such that adding resources actually results in improving the performance or that if redundancy is introduced the system 
performance is not adversely affected. 

A second problem area is that growing a system through scale-out generally results in a system that has to come to terms 
with heterogeneity. Resources in the system increase in diversity as next generations of hardware come on line, as bigger 
or more powerful resources become more cost-effective or when some resources are placed further apart. Heterogeneity means 
that some nodes will be able to process faster or store more data than other nodes in a system and algorithms that rely on 
uniformity either break down under these conditions or underutilize the newer resources.


#### Scalability, Availability, and Stability Patterns
https://www.slideshare.net/jboner/scalability-availability-stability-patterns/46-MasterMaster_Replication

## Scalability Patterns
* Scale up or scale out
* CAP Theorem: at any given point in time you can only pick 2 out of the following 3:
	* Consistency
	* Availability
	* Partition tolerance 

In practice there are only 2 types of systems: 
* **CP** (consistency+avaialability) 
* **AP** (availability+partition tolerance, i.e., sucha system provides eventual consistency as replacement of full consistency).

### Stability Patterns (Patterns for Storing System State)
* **HTTP Caching:** e.g. Acamai
* **RDBMS Sharding:** Sharding - Partitioning +Replicating data among nodes in case some nodes will fail, so that the data will be restored from backup copies. Scaling read requests in RDBMS is hard, scaling write requests is impossible.
* **NoSQL:** based on Distributed Hash Tables, which are scalable, partitioned, fault-tolerant, decentralized, peer-to-peer, popularized. NoSQL DBs: 
	* Google-BigTable (built on top of Google File System)
	* Amazon-Dynamo 
	* SimpleDB 
	* Yahoo-HBase (based on BigTable)
	* Facebook-Cassandra 
	* Linkedin:Voldemort (based on Dynamo).	
	* NoSQL DBs by type:
		* Key-Value: Voldemort, Dynomite
		* Column: Cassandra
		* Document: MongoDB, CouchDB
		* Graph: Neo4J
		* Datastructure: Redis
* **Distributed Caching**
	* Write-through
	* Write-behind
	* Eviction policies: TTL (time to live), bounded FIFO, bounded LIFO, explicit cache invalidation
	* Replication
	* Peer-to-peer: no leader/master, nodes can join and leave at any time
	* Distributed Caching Products: EHCache, JBoss Cache, OSCache. memcached
	* Data Grids/Clustering: parallel data storage, CP/CAP Products: Coherence, Terracotta, GigaSpaces, GemStone, Hazelcast
* **Concurrency**
	* Shared state concurrency
	* Message passing concurrency
	* Dataflow concurrecny
	* Sofware transactional memory *********HERE***** slide 98


## Availability Patterns
* **Fail-over:** the thing you do when normal operation mode can't be done. Once resources are restored back to normal return from fail-over node to normal mode. Not so simple: slide 38, as it takes time to detect failure and switch to fail-over node (that may have failed itself).
* **Replication**
	* **Master-slave:** read/write from/to master node, you can only read from slave node that hold replicated data 
	from the master. 
	* **Tree replication:** tree root is master, its children are either slaves or slave/master that is a slave of the 		root master and serves as a master to its children slvaes.
	* **Master-Master:** all nodea are masters, fully synchronized, can read/wrtie from/to all nodes.
	* **Buddy Replication:** nodes are connected in a ring, where each node stores its own data and backs up data for             its neightbour from left. In case a node fails the nodes update their backup data to suit new ring configuration. 
	  2 modes: push data/pull data.
	
	

