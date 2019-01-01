---
title: "RasPi Cluster (1): Cluster Design"
date: 2015-04-12 16:06:00
tags: [raspi, cluster]
categories: [Raspberry Pi]
---
{% asset_img IMG_3692.jpg %}

## Motivation

Having working on a Raspberry Pi B+ version, I have found that the real limitation is the memory shortage and CPU performance.

With the release of new [Raspberry Pi 2](https://www.raspberrypi.org/raspberry-pi-2-on-sale/), the little board is equipped with ARM-v7 (ARM Cortex-A7) 900MHz (Quad-core), and 1GB memory (shared with GPU), [here](http://en.wikipedia.org/wiki/Raspberry_Pi#Hardware]) is documented specification. By purchasing an MicroSD-HC UHS-I card, we can get a 40MB/s reading speed and around 15-20MB/s writing speed. That is very close to at least a low-end computer.

Therefore, it is possible to create somehow a 3 nodes cluster with low cost (around 200€). The real motivation is to learn how a cluster works and to do some PoC (Proof of Concept) thing.

<!--more-->

## Shopping list

Next step is to find out what we need to build the cluster:

{% asset_img hardware.png %}

Here is the shopping list:

- 3 × Raspberry Pi 2
- 3 × MicroSD 16GB
- 1 × Ethernet Switch / Router
- 3 × USB to Micro USB cable
- 1 × Power Supply
- 1 × [Stackable Raspberry Pi Case](http://www.modmypi.com/raspberry-pi/cases/multi-pi-stacker/multi-pi-stackable-raspberry-pi-case)

Arrival of Raspberry Pi B second generation:

{% asset_img IMG_3693.jpg %}

Arrival of MicroSD 16GB Toshiba:

{% asset_img IMG_3698.jpg %}

## Assembling

The steps are really simple with the instruction from [this post](https://www.modmypi.com/blog/multi-pi-assembly-guide).

{% asset_img IMG_3690.jpg %}
{% asset_img IMG_3691.jpg %}

## Next Step

Burn OS image in Micro SD card, install cluster manager (Mesos/Yarn/...), distributed computing framework (Spark, R, Python, H2O)
