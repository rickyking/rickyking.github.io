---
title: Setting up Python / Jupyterhub for pyspark
tags:
  - ipython
  - spark
  - jupyter
categories:
  - toolbox
date: 2015-12-16 13:23:00
---
In last post, we have set up a `jupyterhub` in CEntOS, the same installation guide can be found for Ubuntu-like system.

In this post, we will discuss how to make `jupyterhub` work with `pyspark` shell.

**Step 1: create a kernel directory in the user's home folder**

```bash
mkdir ~/.ipython/kernels/pyspark
```

**Step 2: create and edit kernel file**

Create & Edit the file: `nano ~/.ipython/kernels/pyspark/kernel.json`

Put the script below into the file:

```
{
 "display_name": "pySpark (Spark 1.4.1)",
 "language": "python",
 "argv": [
  "/usr/bin/python2",
  "-m",
  "IPython.kernel",
  "-f",
  "{connection_file}"
 ],
 "env": {
  "SPARK_HOME": "<spark_dir>",
  "PYTHONPATH": "<spark_dir>/python/:<spark_dir>/python/lib/py4j-0.8.2.1-src.zip",
  "PYTHONSTARTUP": "<spark_dir>/python/pyspark/shell.py",
  "PYSPARK_SUBMIT_ARGS": "--master spark://127.0.0.1:7077 pyspark-shell"
 }
}
```

Please replace the `<spark_dir>` with the location of the spark: `/opt/mapr/spark/spark-1.4.1` in my example.

Put lauching arguments in `PYSPARK_SUBMIT_ARGS`, as shown here create a local `pyspark-shell`, otherwise `--master yarn-client pyspark-shell` in my case for `MapR` cluster.

**Step 3: launch jupyterhub and create a spark notebook with kernel spark-\*.\*.\***

**Optional Step: copy the file to every spark user**
