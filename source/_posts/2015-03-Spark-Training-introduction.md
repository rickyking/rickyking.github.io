---
title: "Spark Training [introduction]"
date: 2015-03-22 12:09:10
tags: [spark, data science]
categories: [Spark]
---

This is a note of [Spark Summit 2014 - Intro to Spark](http://spark-summit.org/2014).

The course materials can be downloaded from [here](https://s3-us-west-2.amazonaws.com/databricks-meng/usb.zip), and the slide can be found [here](http://training.databricks.com/workshop/itas_workshop.pdf).

Video can be found on [youtube](https://youtu.be/VWeWViFCzzg?list=PLTPXxbhUt-YWSgAUhrnkyphnh0oKIT8-j).

<!-- more -->
## Spark Setup

Download [Spark](spark.apache.org/downloads.html), and choose a pre-built version. All should work in win/linux/mac platform.

Once unzipped, use following command to start spark-shell from root of un zipped folder.

```bash
cd spark
./bin/spark-shell
```

Once unzipped, use following command to start pyspark from root of un zipped folder.

```bash
cd spark
./bin/pyspark
```

## Simple Spark App

Spark has two basic opertions: transform (lazy evaluation) & action (trigger transform).

### Word count

The "Hello World" of mapreduce programming: word count. The code in scala is simple:


```scala
val f = sc.textFile("README.md") // read text file
val wc = f.flatMap(l => l.split(" ")).map(word => (word, 1)).reduceByKey(_ + _) // map per word and reduce by key "word"
wc.saveAsTextFile("wc_out") // write out the result in a text file
```

Steps:

- `sc` is the spark context which should be used to initialize all spark transform and action.
- `textFile` a methode to load text file into spark.
- `flatMap` a methode to map the data per line and return the flatten result.
- `map` a methode to map the data per line  and return the corresponding result.
- `reduceByKey` a methode to do the reduce transformation.
- `saveAsTextFile` a mehtode to do the write out action.

The result is in wc_out, by defaut spark create a folder and in it contains serveral results by file.

### Join

The join operation in spark is done by `join` function and exists other functions to do the inner/left/right/outer join.

```scala
val format = new java.text.SimpleDateFormat("yyyy-MM-dd")
case class Register (d: java.util.Date, uuid: String, cust_id: String, lat: Float, lng: Float)
case class Click (d: java.util.Date, uuid: String, landing_page: Int)
val reg = sc.textFile("reg.tsv").map(_.split("\t")).map(
 r => (r(1), Register(format.parse(r(0)), r(1), r(2), r(3).toFloat, r(4).toFloat))
)
val clk = sc.textFile("clk.tsv").map(_.split("\t")).map(
 c => (c(1), Click(format.parse(c(0)), c(1), c(2).trim.toInt))
)
reg.join(clk).collect()
```

Steps:

- create a date parser from java.text
- create class `Register` & `Click`
- read the data "reg.tsv" & "clk.tsv" and parse them, the key is the first element `r(1)`, the map function create a tuple pair of (key, value)
- simply apply `join` methode to `reg` with `clk`

## Essentials


Note that a RDD can be persisted into memory in order to speed up the process.
