---
layout: post
title: "Nginx中log变量及Tengine的log_unescape"
description: ""
categories: []
tags: []
---

### Nginx变量
Nginx的log模块是很多人做日志输出的首选切入点。

Nginx默认```access_log```语法写入服务器本地路径或NFS，用于以后的汇总分析；在此基础上，Tengine增强了日志目标，可以通过组合管道功能或syslog接口进行远程集中日志存储。  

log功能的核心是变量。灵活而全面的变量定义是Nginx日志模块的优势体现。在2011年时曾经整理过一些[```核心变量的意义```](http://mib.cc/technology/2011/10/19/nginx-log_format-abc.html)，主要是第一种类型的核心变量。最近两年核心模块进行过比较大的变动，涌现出比较多的优秀第三方模块，也出现了Tengine/OpenResty这样优秀的第三方发行版，变量的定义大大丰富。

Nginx变量包括：  

1.   核心模块提供了足够的变量定义([```Embedded Variables```](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables))；  
2.   通过rewrite模块的set语法自定义变量（[```rewrite#set```](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#set)）；  
3.   第三方模块提供额外的变量定义(使用ngx_http_add_variable设置一系列ngx_http_variable_t的变量)；  
4.   通过第三方模块做变量内容变换/改写(如[agentzh/set-misc-nginx-module](https://github.com/agentzh/set-misc-nginx-module))。  


在这种情况下做一个```变量大全```需要一定的工作量，根据实际使用情况去查阅各类模块的文档是个捷径。如果有人整理过也请告诉我。

### log_unescape
在实际使用中会遇到一个问题关于日志中被转义的字符，比如输出一段json数据，其中的引号会被转义。这种转义规则参考了[apache httpd的习惯](http://httpd.apache.org/docs/2.2/mod/mod_log_config.html#format-notes)。早在2008年就有[讨论](http://web.archiveorange.com/archive/v/yKUXMZ4X6eg17Nln5WJq)。Nginx官方社区对此问题的态度是继续保留转义。

在实际使用中，查看和处理转义过的字符串带来一些负担，在权衡中选择不做转义也经常是*一种*选择。Tengine在2012年将```log_unescape```的功能加入，可以打开/关闭转义功能，或者仅针对ASCII字符，方便大规模日志处理。

当然escape问题并不只出现在log阶段，比如[这里](http://trac.nginx.org/nginx/ticket/262)，解决方法也不一定都是修改nginx代码或配置——在分析端做转义处理也是一种可行的思路。
