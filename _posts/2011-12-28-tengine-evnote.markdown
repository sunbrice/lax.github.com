---
date: '2011-12-28 17:49:58'
layout: post
slug: tengine-evnote
status: publish
title: Tengine试用手记
wordpress_id: '153'
categories:
- Technology
post_format:
- Standard
tags:
- NC
- Nginx
- Tengine
---

taobao技术团队最近把[Tengine](http://tengine.taobao.org)开放出来了。

Tengine基于目前很流行的Nginx，在Nginx基础上整合了一些第三方模块，并加入taobao团队研发的一些模块。由于Nginx有设计精美的模块机制，使得整合与模块开发非常方便，在此之前taobao团队开放的另一个项目openresty就是在Nginx基础上完成的。

最新版本的tengine基于nginx-1.0.10稳定版（最新的nginx稳定版是1.0.11）。我在试用时加入了人人网系统运维团队开发的[ngx_http_accounting_module](https://github.com/xiaonei/ngx_http_consistent_hash)和[http_upstream_consistent_hash_module](https://github.com/xiaonei/ngx_http_accounting_module)，并按照部署需求进行了RPM包的制作，这个流程与Nginx官方版本一致性很高，几乎不用做修改就能整合到现在的工作流。

可以看出为适应大规模运维的需求，tengine对nginx做了一些修改：



	
  * access_log/error_log支持file/pipe/syslog

	
  * worker_processes和worker_cpu_affinity自动设置

	
  * expires_by_types

	
  * stub_status增加了响应时间

	
  * sysguard

	
  * limit_req和forbid_action




我们关注一下log部分的变化。

tengine对log部分做了较大改变，在原有的文件作为输出目标基础上，增加了syslog和pipe功能。syslog作为日志输出，在nginx-0.6.35时就有过一个patch，但是由于种种原因被拒绝加入官方代码（这可能也是taobao撇开nginx.org另立山头的原因之一）。

syslog的配置语法为：


access_log  syslog:user::debug  main;




access_log  syslog:user::192.168.10.10:514  main;


配置之后需要对syslogd的配置文件做一些修改。虽然部署很方便，但是syslog的方案比较鸡肋，因为现在已经有比较多的更“现代”的方法来进行分布式数据的收集，比如scribe。

使用pipe方式则可以方便地与scribe等系统进行整合。

测试中我使用了更简单的一个方案：通过netcat将管道中的日志发送到远程日志收集端。

配置文件是这样的：


# 将日志实时发送到远程主机192.168.10.10的8000端口，使用TCP协议




access_log   "pipe:/usr/bin/nc  192.168.10.10  8000" main;


在此我选用了便于脚本化且功能强大的netcat。你也可以把其它工具来进行整合，比如与gzip组合使用，进行压缩以降低传输的流量。

pipe方式的日志方式，使得无数想法成为可能，大大提高了运维的灵活性。



关于提到的其它特性更详细的使用说明，可以参考[Tengine](http://tengine.taobao.org)官方网站的文档。

欢迎留言交流。[@我#renren.com](http://www.renren.com/g/277881218) [@我#weibo.com](http://weibo.com/1653644220)
