---
layout: "post"
title: "Install R Package On Amazon Linux"
tags: [r, centos, aws, amazon linux]
categories: [toolbox]
date: "2018-12-20 21:41"
---

Recently, the new company set up everything on AWS. Amazon Linux which is basically Red Hat Linux / Centos, but it uses non-standard folder names.

While I try to install R package which uses a C compiler during installation, the compiler often can't find a file it needs.

As stated in [this thread](https://forums.aws.amazon.com/thread.jspa?threadID=272761), I am trying to install `data.table`, the way to solve it is trying to link the file to the standard location:

```bash
ln -s /usr/lib/gcc/x86_64-amazon-linux/6.4.1/include/omp.h /usr/local/include/
ln -s /usr/lib/gcc/x86_64-amazon-linux/6.4.1/libgomp.spec /usr/lib64/libgomp.spec
ln -s /usr/lib64/libgomp.so.1.0.0 /usr/lib64/libgomp.so
```

It seems this is not an ultimate solution, as each time, we need to find out the non-standard location of the file, and link it. What's more, you need `sudo` to make it happen.

In [this stack overflow thread](https://stackoverflow.com/questions/48307251/aws-linux-server-install-r-package/48568070#48568070), it has a better solution.

We need to first of all, create an `.R` folder under `~/` with a `Makevars` file in it, with the following content (it will tell to R which compiler to use):

```bash
CC = /usr/bin/gcc64
CXX = /usr/bin/g++
SHLIB_OPENMP_CFLAGS = -fopenmp
```

One trick, if you don't have `ssh` on the server, and not really familiar with `bash` editor (`nano`, `vim`), you can edit the file by typing this in R: `file.edit("~/.R/Makevars")`. It will open the file in RStudio.
