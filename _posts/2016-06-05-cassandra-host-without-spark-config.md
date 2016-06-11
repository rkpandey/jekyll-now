---
published: true
title: Cassandra Host outside of Spark Config
layout: post
categories:
  - Analytics
  - big data
  - Cassandra
  - java
  - Scala
  - spark
  - sparksql
tags:
  - big data
  - cassandra
  - cassandra cluster
  - clustering key
  - composite key
  - hbase
  - partition key
  - primary key
---
As mentioned at the [cassandra spark connector help page](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/1_connecting.md), if you want to specify cassandra host and other related configurations, you need to use spark-default.conf or specify it while doing spark submit. Let's say, you have a requirement where you want to read cassandra configs from any other source that too after the spark context is created or simply you need to work with two different cassandra clusters in the same driver code, how do you do it.

One possible way I have identified is to override CassandraConnectionFactory trait. By default, there is DefaultConnectionFactory object that is plugged in as a connection factory implementation. Since it is an object there is not much change we can do...

If you override CassandraConnectionFactory like following:
```scala
class SparkleCassandraConnectionFactory extends CassandraConnectionFactory {
 /* Creates and configures native Cassandra connection */
 override def createCluster(conf: CassandraConnectorConf): Cluster = {
   //one can change conf parameter and push your own host and port...
   DefaultConnectionFactory.clusterBuilder(conf).build()
 }
}
```
Using this approach, you can not only provide host and port for cassandra server but also the authentication info, that you may not like to specify at configuration or as command line parameter due to security issues.
