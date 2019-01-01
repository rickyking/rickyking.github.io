---
title: Configure Pyspark in IPython simple way
tags: [python, spark]
categories: [Spark]
date: 2015-10-13 19:09:32
---

Apache Spark in a distributed in-memory cluster computing system. Many people including me like to use Spark in python with IPython for a data analysis purpose.

But unfortunately the configuration is always a little bit tricky for the moment.

For the complicated way, you can try this [link](http://ramhiser.com/2015/02/01/configuring-ipython-notebook-support-for-pyspark/). Otherwise, use the following python library: [https://github.com/minrk/findspark](https://github.com/minrk/findspark).

Steps to follow:

- Download spark and unzip: `wget http://d3kbcqa49mib13.cloudfront.net/spark-1.5.1-bin-hadoop2.6.tgz && tar -zxvf spark-1.5.1-bin-hadoop2.6.tgz`
- Configure the global variable `SPARK_HOME` to the unzipped folder, don't forget to source the .bashrc or .zshrc.
- The installation is simple by using `pip install findspark`.  
- Get into IPython and play

```python
import findspark
findspark.init()
import pyspark
sc = pyspark.SparkContext(appName="myAppName")
```

That's it, go play with the `SparkContext`.
