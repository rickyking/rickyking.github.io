title: Setting up sparkR in RStudio
tags:
  - r
  - spark
  - rstudio
categories:
  - R
date: 2016-02-19 09:21:00
---
In order to use sparkR in RStudio, we have to call the package installed in `SPARK_HOME`.

Here is a script to be initiated at the beginning of the program:

```r
# launch sparkR in R
Sys.setenv(SPARK_HOME='<spark_dir>')
.libPaths(c(file.path(Sys.getenv('SPARK_HOME'), 'R', 'lib'), .libPaths()))
.libPaths()
library(SparkR)
sc <- sparkR.init(master='yarn-client')
```

Here is an example with my configuration with `Hortonworks HDP`:

```r
# launch sparkR in R
Sys.setenv(SPARK_HOME='/usr/hdp/current/spark-client')
.libPaths(c(file.path(Sys.getenv('SPARK_HOME'), 'R', 'lib'), .libPaths()))
.libPaths()
library(SparkR)
sc <- sparkR.init(master='yarn-client')
```