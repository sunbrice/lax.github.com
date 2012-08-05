---
date: '2010-07-19 19:12:32'
layout: post
slug: dstat-overview
status: publish
title: DSTAT overview
wordpress_id: '8'
categories:
- Technology
tags:
- Dstat
- Linux
---

DSTAT overview


NAME
dstat – versatile tool for generating system resource statistics

SYNOPSIS
dstat [-afv] [options..] [delay [count]]

Samples:

Using dstat to relate disk-throughput with network-usage (eth0), total CPU-usage and system counters:
dstat -dnyc -N eth0 -C total -f 5

Checking dstat’s behaviour and the system impact of dstat:
dstat -taf –debug

Using the time plugin together with cpu, net, disk, system, load, proc and top_cpu plugins:
dstat -tcndylp –top-cpu
this is identical to
dstat –time –cpu –net –disk –sys –load –proc –top-cpu

Using dstat to relate cpu stats with interrupts per device:
dstat -tcyif

Learn more:

1. man dstat
2. http://dag.wieers.com/home-made/dstat/
