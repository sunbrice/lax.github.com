---
date: '2011-10-19 19:21:03'
layout: post
slug: nginx-log_format-abc
status: publish
title: Nginx log_format设置ABC
wordpress_id: '134'
categories:
- Technology
tags:
- log_format
- Nginx
---

nginx作为反向代理服务器时，打开access_log可以用来查找后端的问题。

默认的nginx.conf有一个```log_format```配置。这个配置对于普通的静态服务器有些帮助，但是作为代理，还需要做一些定制。


    log_format	main	'$host $remote_addr - $remote_user [$time_local] $request '
				'"$status" $body_bytes_sent "$http_referer" '
				'"$http_user_agent" "$http_x_forwarded_for"';


我们使用nginx作为反向代理，代理后端部署的实际服务地址可能不止一个（一般情况下是这样）。我们要了解后端服务器的响应情况，就需要在log_format中增加几个变量。

下面列出几个重要的变量：


*   ```$status```  ```$upstream_status``` :  响应的状态码（响应正常还是出错了？）复习一下[```HTTP CODE```](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)  
*   ```$request_time```  ```$upstream_response_time``` :  关注性能必备！看看前天传输文件的时间，以及后台处理的时间。前者对了解网络状态有益，后者对了解服务性能有益。  
*   ```$remote_addr```  ```$upstream_addr``` :  用户的IP地址，后端服务的IP地址  
*   ```$bytes_sent``` ```$body_bytes_sent```  :  发送给用户端的数据量（包含HTTP头和不包含HTTP头）  
*   ```$host``` ```$request``` :  最重要的，搞定具体域名具体URL，不抓瞎。


根据实际情况，把以上的变量组合放进log_format中。
