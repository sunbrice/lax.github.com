---
layout: post
title: "awk统计脚本中的排序方法"
description: ""
categories: [Technology]
tags: [awk]
---
{% include JB/setup %}

最近在使用`awk`工具进行统计时，经常需要用的哈希来保存临时结果，同时在输出前需要根据哈希的key来进行排序。

以前在`awk`脚本采取的方式是先通过`asorti`对key进行一次排序，生成一个排序的key列表，然后遍历这个列表，进行输出操作。这个过程会增加几行代码，同时使得代码的可读性下降，不易理解。

`awk`在排序这方面的文档是比较缺乏的，在翻阅资料是发现，设置环境变量`WHINY_USERS=1`，可以使遍历任何哈希时根据key的排序顺序进行。这种方式减少了代码量，不过需要提前设置环境变量。
`Hadoop`的`Streaming`方式执行脚本，可以通过添加选项`-cmdenv WHINY_USERS=1`来传递环境变量。

注：`awk`中哈希的key默认是字符串类型，排序基于字符串顺序。考虑0,1,12,2,21的顺序。
