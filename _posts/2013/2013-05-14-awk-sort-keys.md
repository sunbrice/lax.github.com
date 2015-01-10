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


### 更新 UPDATED [20150110]

`awk` 版本4之后，去掉了对 `WHINY_USERS` 的支持。替代方法是在 `BEGIN` 阶段设置全局的设置：`PPROCINFO["sorted_in"]`。

如：

    BEGIN {
      PPROCINFO["sorted_in"] ＝ "@ind_str_asc"
    }

可选的值有：

*   "@unsorted"
    不排序

*   "@ind_str_asc" / "@ind_str_desc"
    index按字符串顺序

*   "@ind_num_asc" / "@ind_num_desc"
    index数字顺序

*   "@val_type_asc" / "@val_type_desc"
    value类型排序（升序：数字 < 字符串 < 数组）

*   "@val_str_asc" / "@val_str_desc"
    value按字符串顺序

*   "@val_num_asc" / "@val_num_desc"
    value按数字顺序


举个例子：

    echo '1
    2
    3
    4
    5
    1
    2
    3
    5
    7
    3
    2
    1
    1' | awk 'BEGIN { PROCINFO["sorted_in"] = "@ind_num_asc" } { a[$1] += 1 } END {for(n in a){ print n } }'     

输出：

    1
    2
    3
    4
    5
    7

*   https://www.gnu.org/software/gawk/manual/html_node/Controlling-Scanning.html
