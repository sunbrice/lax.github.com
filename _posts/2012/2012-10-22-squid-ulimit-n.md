---
layout: post
title: "ulimit -n的重要性"
description: ""
categories: [Technology, Linux]
tags: [ulimit, squid]
---

最近发现有几个squid实例的稳定性成问题，对比了环境设置，分析了几个可能的因素。

1    由于squid前使用了nginx，为了减少连接开销，开启了keepalive连接。keepalive机制存在一些问题，如客户端不停止发送请求，nginx不会中止与后端服务的连接，这将造成squid的访问压力无法在短时间内调整。不过分析了这个情形，问题不全在这里。

2    发现连接数的最高值稳定在2000左右。看到这个数字，寻思会不会与连接数限制有关。曾经遇到过memcached由于默认1024个连接数用完而不提供服务的现象，吞吐率在瞬间降低为0。由于squid与nginx跑在同一台服务器上，2k恰好约对应squid的1024个连接数。

基于假设2，查看了squid进程的limits。

 [root@BJCER210-63 ~]# cat /proc/17174/limits 
 Limit                     Soft Limit           Hard Limit           Units     
 Max cpu time              unlimited            unlimited            seconds   
 Max file size             unlimited            unlimited            bytes     
 Max data size             unlimited            unlimited            bytes     
 Max stack size            8388608              unlimited            bytes     
 Max core file size        0                    unlimited            bytes     
 Max resident set          unlimited            unlimited            bytes     
 Max processes             128577               128577               processes 
 Max open files            1024                 4096                 files     
 Max locked memory         65536                65536                bytes     
 Max address space         unlimited            unlimited            bytes     
 Max file locks            unlimited            unlimited            locks     
 Max pending signals       128577               128577               signals   
 Max msgqueue size         819200               819200               bytes     
 Max nice priority         0                    0                    
 Max realtime priority     0                    0                    
 Max realtime timeout      unlimited            unlimited            us        

果然Max open files只有默认的1024。查看另外一台更稳定的squid服务器，发现这个值都是65535。

于是准备了一个测试环境，验证了squid启动时设置ulimit -n的重要性：squid后端使用nginx做源并使用nginx-delay设置延时5s，squid进程启动前不设置ulimit及设置为65535，增加并发数，查看各自的表现。

*    ulimit -n为系统默认1024时，并发数达到800左右吞吐量下降到接近零；
*    设置ulimit -n 65535时，并发数增加吞吐量亦增加。

假设得到证明，可以推断后端性能变差时，由于可用文件数用完而无法开启新的连接。ulimit -n需要设置足够大才能保证squid的稳定。
