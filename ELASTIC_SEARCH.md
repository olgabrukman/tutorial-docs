
[Basic consepts](https://www.tutorialspoint.com/elasticsearch/elasticsearch_basic_concepts.htm)

Elasticsearch is an Apache Lucene-based search server. Elasticsearch is a real-time distributed and open source full-text search and analytics engine. ES is accessible from RESTful web service interface and uses JSON documents to store data. It is built on Java programming language, which enables Elasticsearch to run on different platforms. It enables users to explore very large amount of data at very high speed.

ES replaces MongoDB, RavenDB. Elasticsearch uses denormalization to improve the search performance.
Elasticsearch is one of the popular enterprise search engines

Key Concepts
===============================================
* Node − It refers to a single running instance of Elasticsearch. Single physical and virtual server accommodates multiple 
nodes depending upon the capabilities of their physical resources like RAM, storage and processing power.
* Cluster − It is a collection of one or more nodes. Cluster provides collective indexing and search capabilities 
across all the nodes for entire data.
* Index − It is a collection of different type of documents and document properties. Index also uses the concept 
of shards to improve the performance. For example, a set of document contains data of a social networking application.
* Type/Mapping − It is a collection of documents sharing a set of common fields present in the same index. For example, 
an Index contains data of a social networking application, and then there can be a specific type for user profile data, 
another type for messaging data and another for comments data.
* Document − It is a collection of fields in a specific manner defined in JSON format. Every document belongs to a 
type and resides inside an index. Every document is associated with a unique identifier, called the UID.
* Shard − Indexes are horizontally subdivided into shards. This means each shard contains all the properties of document, 
but contains less number of JSON objects than index. The horizontal separation makes shard an independent node, which can be store in any node. Primary shard is the original horizontal part of an index and then these primary shards are replicated into replica shards.
* Replicas − Elasticsearch allows a user to create replicas of their indexes and shards. Replication not only helps in increasing the availability of data in case of failure, but also improves the performance of searching by carrying out a parallel search operation in these replicas.


Why ElasticSearch
===============================================
* Elasticsearch is developed on Java, which makes it compatible on almost every platform.
* Elasticsearch is real time, in other words after one second the added document is searchable in this engine.
* Elasticsearch is distributed, which makes it easy to scale and integrate in any big organization.
* Creating full backups are easy by using the concept of gateway, which is present in Elasticsearch.
* Handling multi-tenancy is very easy in Elasticsearch when compared to Apache Solr.
* Elasticsearch uses JSON objects as responses, which makes it possible to invoke the Elasticsearch server with a large number of different programming languages.
* Elasticsearch supports almost every document type except those that do not support text rendering.


Comparison between Elasticsearch and Relational DB
======================================================

Elasticsearch| RDBMS       
-------------|---------
Index |	Database   
Shard | Shard      
Mapping	| Table      
Field | Field      
JSON Object | Tuple      


Java  API
===============================================
[API](https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html)
### Node client
The node client joins a local cluster as a non data node. In other words, it doesn’t hold any data itself, 
but it knows what data lives on which node in the cluster, and can forward requests directly to the correct node.

### Transport client
The lighter-weight transport client can be used to send requests to a remote cluster. It doesn’t join 
the cluster itself, but simply forwards requests to a node in the cluster.

Both Java clients talk to the cluster over port 9300, using the native Elasticsearch transport protocol. 
The nodes in the cluster also communicate with each other over port 9300. If this port is not open, 
your nodes will not be able to form a cluster.
Elasticsearch is document oriented, meaning that it stores entire objects or documents. It not only stores them, 
but also indexes the contents of each document in order to make them searchable. In Elasticsearch, you index, 
search, sort, and filter documents—not rows of columnar data. This is a fundamentally different way of thinking 
about data and is one of the reasons Elasticsearch can perform complex full-text search.


ELASTIC SEARCH QUERIES
===============================================

[Queries Tutorial](https://www.elastic.co/guide/en/elasticsearch/guide/current/combining-filters.html)

```ssh
#add entry to to index test, table obj
curl -XPOST 'https://localhost:9200/test/obj/2' --cert /opt/ncso/var/lib/mft/ElasticSearch/cert.pem  --key /opt/ncso/var/lib/mft/ElasticSearch/key.pem --insecure -H 'Content-Type: application/json' -d '{"a":{"p1":"25", "p2":"150"}, "b":{"p1":"150", "p2":"150"}}'
```
```ssh
//see all objects
curl -XGET 'https://localhost:9200/test/_search' --cert /opt/ncso/var/lib/mft/ElasticSearch/cert.pem  --key /opt/ncso/var/lib/mft/ElasticSearch/key.pem --insecure
```

```ssh
//search 
curl -XGET 'https://localhost:9200/test/_search' --cert /opt/ncso/var/lib/mft/ElasticSearch/cert.pem  --key /opt/ncso/var/lib/mft/ElasticSearch/key.pem --insecure -H 'Content-Type: application/json' -d '{"query" : {"bool" : {"must" : {"match" : { "range" : { "a.p1" : {"gt" : 20 }}}}}}}'
```

```ssh
#working range on 1 property
curl -XGET 'https://localhost:9200/test/_search' --cert /opt/ncso/var/lib/mft/ElasticSearch/cert.pem  --key /opt/ncso/var/lib/mft/ElasticSearch/key.pem --insecure -H 'Content-Type: application/json' -d '{"query" : {"range" : { "a.p1" : {"gt" : 20 }}}}'

curl -XGET 'https://localhost:9200/test/_search' --cert /opt/ncso/var/lib/mft/ElasticSearch/cert.pem  --key /opt/ncso/var/lib/mft/ElasticSearch/key.pem --insecure -H 'Content-Type: application/json' -d '{"query" : {"bool": {"must": [{"range" : { "a.p1" : {"lt" : 21 }}},{"range": {"b.p1" : {"gt": 150}}}]}}}'
```            
			
			
			
			
			
			
			
