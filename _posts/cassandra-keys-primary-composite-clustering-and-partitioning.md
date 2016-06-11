---
published: false
layout: post
title: Cassandra keys : primary, composite, clustering and partitioning
categories:  
 - Analytics
 - big data
 - Cassandra
 - java
 - Scala
 - spark
 - sparksql
tags: big data, cassandra, cassandra cluster, clustering key, composite key, hbase, partition key, primary key 
---
When I started reading [Cassandra docs](http://cassandra.apache.org/), my biggest source of confusion started with all different types of keys that Cassandra has. I will give you the simplified view of all types of keys here with CQL examples.

The primary key is a general concept to indicate one or more columns used to retrieve data from a Table.

The primary key may be SIMPLE.

```sql
create table SimplePrimaryKey (
  key text PRIMARY KEY,
  data text
);
```

That means that it is made by one single column.

But primary key can be COMPOSITE too, generated from more than one column.

```sql
create table CompositePrimaryKey (
      key_part_one text,
      key_part_two int,
      data text,
      PRIMARY KEY(key_part_one, key_part_two)      
  );
```

In a situation of <strong><em>COMPOSITE</em></strong> primary key, the "first part" of the key is called <strong><em>PARTITION KEY</em></strong> (in this example <strong>key_part_one</strong> is the partition key) and the second part of the key is the <strong><em>CLUSTERING KEY</em></strong> (<strong>key_part_two</strong>).

But in general sense these concepts are defined as following:
<ul>
 	<li>The <strong>Partition Key</strong> is responsible for row distribution across your nodes. That is, data is partitioned based on this key.</li>
 	<li>The <strong>Clustering Key</strong> is responsible for data sorting within the partition. That is, rows are sorted based on this key withing each partition.</li>
 	<li>The <strong>Primary Key</strong> is equivalent to the <strong>Partition Key</strong> in a single-field-key table.</li>
 	<li>The <strong>Composite/Compound Key</strong> is just a multiple-columns key.</li>
</ul>
