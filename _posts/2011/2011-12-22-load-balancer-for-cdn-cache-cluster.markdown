---
date: '2011-12-22 00:45:27'
layout: post
slug: load-balancer-for-cdn-cache-cluster
status: publish
title: 关于选择缓存集群负载均衡调度算法的一些想法
wordpress_id: '148'
categories:
- Technology
tags:
- CDN
- Nginx
- 一致性哈希
- 缓存
- 负载均衡
---

最近两天我维护的一组缓存服务器遇到一些情况。在处理过程中对集群的调度算法进行了一些思考。欢迎各位看官拍砖。



先说一下背景。

这个缓存集群有30多台缓存节点，缓存目标是大小为2-5k的图片文件。

最早的部署方案是将这些缓存节点分成六组（G1-G6），每组4-6个节点。根据文件名特征，将每个文件对应到六组之一；而在组内则采取轮询的方式选择节点进行访问，比较随机。
访问文件在选择的节点中MISS时，选择的缓存节点会从源站或同组内其它节点读取文件，放入自己的缓存，并返回给查询方。
当节点访问出错时（如DOWN机，断网等），文件访问的发起方被轮询算法调度到同组的另外一个节点。
这种方案会造成同组内保存了同一文件的多个副本，这既是好事有事坏事：好的方面是能解决单个缓存节点失效的问题；不好的方面是重复文件使集群的空间利用率不高，对源站造成一定的压力。



之后开始实施了一种改进方案，在负载均衡器中使用一致性哈希算法进行调度。一致性哈希的简单原理为：
1、所有缓存节点通过一种哈希算法计算，根据哈希值被分布到一个虚拟的环上，每个缓存节点对应环中的一个节点（改进的算法中会生成多个节点，以提高随机性）；
2、将查询的文件名使用相同的哈希算法计算，得到索引值；
3、使用索引值在环上查找。如果索引值正好是一个节点的值，则直接选择对应的缓存节点；否则从索引值所在位置顺着环的方向寻找下一个缓存节点。
4、根据上一步选择的结果，对相应缓存节点进行文件存取操作。



根据实际部署需求我们在一个开源的负载均衡模块进行开发。由于缓存节点的能力基本均等，我们通过多组测试选择了更好的哈希算法来提高均衡性，防止个别节点过热；为了处理节点失效的情形，我们开发了响应的调度功能，当某个缓存节点失效时，环上相邻缓存节点会被选择。

至此，这种方案的雏形就完成了。既能解决有效利用容量的需求，又能处理节点失效。由于使用一致性哈希后不在需要进行分组管理，这种方案也带来运维成本的降低。



（未完）
>>多个负载均衡器的情况下，缓存失效时的调度算法
