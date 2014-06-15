---
layout: post
title: "Ruby中的map方法和reduce方法"
description: ""
categories: [Ruby]
tags: [Ruby, map, reduce]
---
{% include JB/setup %}

有同学问到一个问题

    1
    2
    3
    4
    5
    6
    ...
    
    每3行加起来，结果为
    
    6
    15 
    ...

这个问题，处理过程分为两步，先将输入的数字每三个分为一组，然后对每组执行累加，输出结果。

传统面向过程的写法需要进行多次循环，有一些判断条件。读者可以自行写一下，用任何语言实现。

这两个过程，第一步叫做分块，对每一个元素进行操作；第二步则是将结果合并后输出结果。数学上的名字叫map和reduce。看到这个名字你肯定想到了hadoop——没错，就是那个map和reduce，只不过hadoop做了一些工程上的优化。
关于map/reduce，如果不了解，可以参考(这里)[http://www.haskell.org/haskellwiki/MapReduce_as_a_monad]和(这里)[http://en.wikipedia.org/wiki/MapReduce]。

Ruby中有相应的方法，属于Enumerable模块，对于支持Enumerable的类（如Array）可以直接使用。

直接写出答案如下：

    # 首先从分块，执行map操作；然后对每个map内部执行reduce
    num_list = (1..100)
    num_list.each_slice(3).map {|b| b.reduce(:+)}

细心的你会发现这与hadoop的概念有些差异，reduce方法的对象不是map的结果集。没错，这个reduce是在map内部执行的。

其实，map和reduce对应于对数据集的两种操作，map/reduce风格只是这两种操作组合使用的一个特例，reduce是可以独立于map而单独使用的。

再来看一个hadoop风格reduce的例子

    # puts是reduce执行的方法
    num_list.each_slice(3).map {|b| b.reduce(:+)}.reduce(nil, :puts)

体会一下差异。

再来看一个例子：
    # 求平方和
    num_list = (1..100)
    (1..100).reduce {|total, element| total += element * element}     => 338350
