---
layout: post
title: "[阅读] 基于P2P的CDN/并发"
description: ""
categories: []
tags: []
---
{% include JB/setup %}

### [PeerCDN](http://peercdn.com)

利用P2P进行文件分发不是什么新鲜事，但是在Web环境引入P2P仍然有很多挑战。PeerCDN团队正在进行相关技术的研发。该项目期望通过P2P技术为网站提供稳定、便捷、低成本的内容分发方案。

任何支持WebRTC的浏览器可以支持这种技术，这比之前需要安装额外软件的技术是一个显著的进步。网站页面中只需要加入一行javascript代码。

PeerCDN的专注点应该是大文件（视频，音频，图片），而对于海量小文件则可能会力不从心。

[PeerCDN](http://peercdn.com)

### [Solving the wrong problem](http://joearms.github.com/2013/03/28/solving-the-wrong-problem.html)

漫长的计算机发展史，芯片规模扩展和时钟频率提升是一个主旋律，奠定了摩尔定律的基础。摩尔定律对软件发展的意义在于，大部分串行执行的程序，可以获得越来越短的执行时间。

从2004年开始，芯片的发展有了微妙的变化：芯片规模仍然越来越大，开始增加更多的核心，而同时时钟频率并没有继续提升。这种情况颠覆了对软件开发的一些基本的认识。

有现实的例子为证，当一个单线程的C程序遇到支持多线程的Erlang程序，性能高下不再是底层预言获胜，而是能够利用多核提升效率的一方。

[Joe Armstrong的Solving the wrong problem](http://joearms.github.com/2013/03/28/solving-the-wrong-problem.html)

### [Concurrency Models: Go vs Erlang](http://joneisen.me/post/38188396218)

一个新手对比了Go和Erlang这两种支持并发的语言。Go采用goroutine，有共享内存，因此在多核上扩展，通过channel方式传递消息，需要进行手工的错误处理；Erlang采用进程模型，不能共享内存，因此可以做多机扩展，通过mailbox方式传递消息，语言自身支持异常，通过OTP实现各种稳定性部署。

[Concurrency Models: Go vs Erlang](http://joneisen.me/post/38188396218)



