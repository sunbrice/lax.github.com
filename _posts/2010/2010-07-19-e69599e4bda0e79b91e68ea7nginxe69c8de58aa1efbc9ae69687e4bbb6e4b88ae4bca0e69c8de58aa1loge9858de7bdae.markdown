---
date: '2010-07-19 19:14:30'
layout: post
slug: '%e6%95%99%e4%bd%a0%e7%9b%91%e6%8e%a7nginx%e6%9c%8d%e5%8a%a1%ef%bc%9a%e6%96%87%e4%bb%b6%e4%b8%8a%e4%bc%a0%e6%9c%8d%e5%8a%a1log%e9%85%8d%e7%bd%ae'
status: publish
title: 教你监控Nginx服务：文件上传服务log配置
wordpress_id: '12'
categories:
- Technology
tags:
- Nginx
---

教你监控Nginx服务：文件上传服务log配置


    log_format upload ‘[$time_local] $status $host $upstream_addr $upstream_response_time '
                      '$request_time $remote_addr $request_length $bytes_sent’;
    access_log logs/upload.log upload;

变量说明：

| Variable | 含义 |
|----------|------|
| $time_local | 本地时间|
| $status | 状态码|
| $host | 主机名|
| $upstream_addr | 后端地址|
| $upstream_response_time | 后端响应时间|
| $request_time | 请求时间|
| $remote_addr| 用户地址|
| $request_length | 请求长度|
| $bytes_sent | 返回长度|
