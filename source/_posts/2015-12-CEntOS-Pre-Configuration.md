---
title: CEntOS Pre-Configuration
tags: [centos, R]
categories: [toolbox]
date: 2015-12-16 09:07:43
---

CEntOS (the community enterprise operating system) is based on RedHat distribution of Linux. The reputation of the stability makes it a first choice for enterprise grade server.

Here is some simple configuration before compiling and other installation.

**Add EPEL repo**

Check [here](https://fedoraproject.org/wiki/EPEL) for the latest `rpm`.

```
su -c 'rpm -Uvh http://download.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm'
```

**Install Dev Tools**

```
yum group install "Development Tools"
```

**R and its friends**

According to [this](https://cran.r-project.org/bin/linux/redhat/README), we the installation of R is as simple as `yum install -y R`. For RStudio and Shiny-Server, considering [this by selecting RedHat/CentOS](http://www.rstudio.com/products/rstudio/download-server/) and [this by selecting RedHat/CentOS](http://www.rstudio.com/products/shiny/download-server/).
