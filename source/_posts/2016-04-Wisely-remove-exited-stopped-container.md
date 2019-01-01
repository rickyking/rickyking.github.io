---
title: Wisely remove exited/stopped container
tags: [docker]
categories: [docker]
date: 2016-04-08 16:11:36
---

Recently I have learned hard to use docker, in order to take advantage of the latest technology advance.

One problem is after doing a lot of experiments, I have many containers left on exited status `docker ps -a` and the normal `docker rm [container ID]` cannot work efficiently.

So keep in memory the following code which can remove all exited containers gracefully: `docker rm -v $(docker ps -a -q -f status=exited)`.
