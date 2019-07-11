## Collecting and Storing Metrics 
[Graphite - tool for storing numeric time-series data](https://graphite.readthedocs.io/en/latest/overview.html).

### CollectD
CollectD is statistics collection daemon. It periodically polls various sources 
(the plugin collection is quite exhaustive) for metrics. It can receive metrics as well. 
CollectD is well versed in collecting metrics from the system, various popular services 
(web servers, database servers, etc), as well as custom application metrics 
(this requires applications to generate those metrics). CollectD can do more things as well. 

### StatsD
StatsD is event counter/aggregation service. It receives per-event timings and calculates and 
generates aggregated values. It is built to count/aggregate custom applications metrics. 
It only requires the application to send events, without any need for aggregations.

StatsD is a tool to aggregate many data-points, thus dramatically reducing the load on the backend storage system. 
StatsD, in addition to being an aggregation server, also has client libraries in practically any programming language. 
This makes it a very useful tool for sending custom service metrics to graph-plotting servers, such as Graphana.
https://github.com/etsy/statsd
https://thenewstack.io/collecting-metrics-using-statsd-a-standard-for-real-time-monitoring
https://github.com/etsy/statsd/wiki#client-implementations

### Prometheus
Prometheus is another collection tool. Prometheus is newer than StatsD.
[Prometheus vs. StatsD](https://medium.com/@yuvarajl/prometheus-vs-statsd-for-metrics-collection-3b107ab1f60d)

### Metric Types
1. Standard, collected by CollectD:
    1. Server metrics: CPU, memory, networking
    1. Middleware metrics(Docker, RabbitMQ, Kafka, CouchBase, Aerospike...)
2. Custom, generated by by writing custom code. StatsD is used to send custom metrics to Graphite. Custom metrics examples:  
    * How much time does an attribution take? 
    * How many in-app events were sent to AppsFlyer for application X? 

### Grafana
Now that our metrics have been collected, sent, and stored in graphite, its time to see them.  
To do that we use a very popular tool called Grafana. 
[Grafana](https://www.youtube.com/watch?v=sKNZMtoSHN4&index=7&list=PLDGkOdUX1Ujo3wHw9-z5Vo12YLqXRjzg2) 
is a visualisation tool that integrates with many data stores - Graphite being one of them. 
It provides the ability to construct dashboards that display time-series data in a very human friendly way.



