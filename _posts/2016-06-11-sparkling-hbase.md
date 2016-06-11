---
published: false
---
---
layout: post
title: Sparkling HBase
---

## Sparkling HBase

Several abstractions have come along to make cloud-scale analytics easier. One of them is [Apache Spark](http://spark.apache.org). Spark is the one of the most active project of apache. It utilizes cluster memory to minimize the disk reads and writes that slow down MapReduce. Once you get past the learning curve, I don’t see how anyone would prefer Java to Scala for analytics.

Please take a look at the [Spark documentation](http://spark.apache.org/docs/latest/scala-programming-guide.html) to learn about the [RDD](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD) abstraction. RDD’s are basically fancy arrays. You load data into them and perform whatever combination of operations—maps, filters, reducers, etc.— you want. RDD’s make analytics so much more fun to write than canonical MapReduce. In the end, Spark doesn’t just run faster; it lets us write faster.

This blog post is dedicated on how to create RDD’s from HBase, the Hadoop database. HBase is just a cloud scale key-value store.

RDDs can be created from Hadoop InputFormats as spark documentation claims. You may know that [InputFormat](http://hadoop.apache.org/docs/current/api/org/apache/hadoop/mapred/InputFormat.html) is the Hadoop abstraction for anything that can be processed in a MapReduce job. As it turns out, HBase uses a [TableInputFormat](http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/mapreduce/TableInputFormat.html), so it should be possible to use Spark with HBase.

It turns out that it is.

The [NewHadoopRDD](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.NewHadoopRDD) is great for reading data stored in later versions of Hadoop, which is exactly what we need here.

Check out this code. {% gist 15dce0a36f454ec3e192 %}


Once we instantiate the [SparkContext](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.SparkContext) for the local machine, we write an anonymous function to create an [HBaseConfiguration](http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/HBaseConfiguration.html), which will enable us to add the HBase configuration files to any Hadoop ones we have as well as the name of the table we want to read. That’s an HBase thing—not a Spark thing.

Then we call our anonymous function with the location of [hbase-site.xml](http://hbase.apache.org/book/config.files.html) and the name of the table we want to read, _table-with-data_, to provide Spark with the necessary HBase configuration to construct the RDD. Once we have the RDD, we can perform all the usual operations on HBase we usually see with the more conventional usage of RDD’s in Spark.

When we create RDD from a HBase table, it usually involves full table scan. HBase, by default is not optimized for full table scans. HBase is optimized for random reads. You don't want to be using block cache while doing full table scans as it will be constantly outdated. So you need to use _setCacheBlocks(false)_.

More importantly, try setting CACHE as high as you can.  This controls how many rows are returned per RPC call.  The default may be 1, which would be a lot of RPC calls.  The limiting factor for CACHE is memory, as you need to be able to fit the entire response in memory on both the server and client side.  So use that to estimate as high a cache setting as possible. You can use _setCaching(5000)_.

I played around block size also. But I learned the hard way that block size has no bearing on the amount of work that HBase does for a full scan.

In next blog post I will cover how to create [DataFrame](https://spark.apache.org/docs/latest/api/scala/org/apache/spark/sql/DataFrame.html) from a HBase table.
