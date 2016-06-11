---
published: true
layout: post
title: 'Cassandra keys : primary, composite, clustering and partitioning'
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
When I started reading [Cassandra docs](http://cassandra.apache.org/), my biggest source of confusion started with all different types of keys that Cassandra has. I will give you the simplified view of all types of keys here with CQL examples.

The primary key is a general concept to indicate one or more columns used to retrieve data from a Table.

The primary key may be **_SIMPLE_**.

```sql
create table SimplePrimaryKey (
  key text PRIMARY KEY,
  data text
);
```

That means that it is made by one single column.

But primary key can be **_COMPOSITE_** too, generated from more than one column.

```sql
create table CompositePrimaryKey (
      key_part_one text,
      key_part_two int,
      data text,
      PRIMARY KEY(key_part_one, key_part_two)      
  );
```

In a situation of **COMPOSITE** primary key, the "first part" of the key is called **_PARTITION KEY_** (in this example **key_part_one** is the partition key) and the second part of the key is the **_CLUSTERING KEY_** (**key_part_two**).

But in general sense these concepts are defined as following:
* The **Partition Key** is responsible for row distribution across your nodes. That is, data is partitioned based on this key.
* The **Clustering Key** is responsible for data sorting within the partition. That is, rows are sorted based on this key withing each partition.</li>
* The **Primary Key** is equivalent to the **Partition Key** in a single-field-key table.
* The **Composite/Compound Key** is just a multiple-columns key.
