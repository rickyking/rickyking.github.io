---
title: 'NiFi Zeppelin Spark - CitiBike Station Feed Wrangling'
tags:
  - spark
  - nifi
  - zeppelin
  - citibike
  - bikesharing
categories:
  - Spark
date: 2016-01-21 17:09:00
---
[Apache NiFi](https://nifi.apache.org) supports powerful and scalable directed graphs of data routing, transformation, and system mediation logic.

Here I will use NiFi to create a 30 seconds scheduler to retrieve the CitiBike's [Station Feed](http://www.citibikenyc.com/stations/json). The data is in the JSON format:

{% asset_img NiFi_0.png %}

<!--more-->

## Install NiFi

We can get NiFi [installation file](https://nifi.apache.org/download.html) and then just unzip the file, start with the daemon.

```
wget http://www.us.apache.org/dist/nifi/0.4.1/nifi-0.4.1-bin.tar.gz
tar -xzf nifi-0.4.1-bin.tar.gz #unzip
cd nifi-0.4.1
sudo ./bin/nifi.sh install #install as a service
sudo service nifi start #start service
```

Then we can get to the NiFi interface with [http://your-ip-addresse:9090/nifi]() (9090 is the default port).

Get the NiFi [documentation](https://nifi.apache.org/docs.html) and watch the [video](https://nifi.apache.org/videos.html) to learn more about NiFi.

I have created the following schema to schedule a 30 seconds cron job to get the [json file](http://www.citibikenyc.com/stations/json).

{% asset_img NiFi_1.png %}

Data Flow:

1. `GetHTTP`: this processor is created with the 30 sec timer driven scheduler and get the data from the citibike's site, the flow file is called `data.json`.
2. getDataTime: this is a `EvaluateJsonPath` processor which gets the timestamp information within the json file by evaluating the json path `$.executionTime`. This information will be written to the flow file's new attribute: `datetime`. And the destination property should be set to flowfile-attribute. Self terminate: unmatched & failure.
3. getContent: This `EvaluateJsonPath` processor will get the content of the flow file `$.stationBeanList` and overwrite it. The destination remains unchanged, and create a new property `JsonPaths`, the value is `$.stationBeanList`. Self terminate: unmatched & failure.
4. `UpdateAttribute`: the processor will rename the flow file according to the newly created attribute `datetime`, the property `filename`: `${datetime:toDate('yyyy-MM-dd hh:mm:ss a'):format('yyyyMMdd_HHmmss')}.json` and create a `folder` attribute as well `${datetime:toDate('yyyy-MM-dd hh:mm:ss a'):format('yyyyMMdd')}`.
5. `LogAttribute`: is used the visualize the change.
6. `DetectDuplicate`: the processor helps to identify and remove the duplicated JSON flowfile with the `Cache Entry Identifier` - `${datetime}`. (Distributed Cache Service should be spawned up accordingly)
7. `PutHDFS`: the writing processor to HDFS, we can use other local file system in place. `Hadoop Configuration Resources` is set to `/etc/hadoop/conf/core-site.xml` and the directory is set to `/bikesharing/NYC/${folder}`.

PS: `${}` is the Apache NiFi Expression Language, see [here](https://nifi.apache.org/docs.html).

## Wrangle the JSON data

Next we will use the spark to read/wrangle the JSON, and store as `parquet` data in HDFS.

In [Zeppelin](https://zeppelin.incubator.apache.org), we can easily use `pyspark` engine to do the job, here is the script.

```python
from datetime import date, timedelta
from pyspark.sql import functions as f
from pyspark.sql.types import TimestampType
#only working on D-1
d = timedelta(days=1)
date_today = date.today()-d
#Read data
nyc = sqlContext.read.json('/bikesharing/NYC/'+date_today.strftime("%Y%m%d"))
#Convert datetime information
nyc_convert = nyc.withColumn('datetime', f.from_unixtime(f.unix_timestamp(nyc.lastCommunicationTime, 'yyyy-MM-dd hh:mm:ss a')).cast(TimestampType())) \
				 .withColumn('year', f.year('datetime')) \
				 .withColumn('month', f.month('datetime')) \
				 .withColumn('dayofmonth', f.dayofmonth('datetime')) \
				 .withColumn('hour', f.hour('datetime')) \
				 .withColumn('minute', f.minute('datetime')) \
				 .withColumn('second', f.second('datetime')) \
				 .withColumn('weekofyear', f.weekofyear('datetime'))
#remove duplicated rows
nyc_unique = nyc_convert.dropDuplicates()
nyc_unique.cache()
#write to parquet with mode append
nyc_unique.write.partitionBy('year').parquet('/bikesharing/NYC_parquet', mode='append')
```

*This script is scheduled at 3am each day to consolidate the data of yesturday.*

Another line of script to get the whole parquet data back for analysis: `nyc = sqlContext.read.parquet('/bikesharing/NYC_parquet')`.

{% asset_img NiFi_2.png %}

{% asset_img NiFi_3.png %}
