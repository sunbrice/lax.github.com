---
date: '2010-08-11 19:36:00'
layout: post
slug: twitter%e7%9a%84%e5%bf%ab%e9%80%9f%e7%a8%8b%e5%ba%8f%e9%83%a8%e7%bd%b2
status: publish
title: Twitter的快速程序部署
wordpress_id: '20'
categories:
- Technology
tags:
- deploy
- twitter
---

Twitter早期的更新采用Capistrano和Git，每次程序更新时间与服务起数目呈线性关系。当服务器数量迅速增加时，更新       效率开始成为问题。
基于中心代码库，更新时代码库压力较大。通过增加Git库的副本，可以缓解问题，但是从长期看，需要持续进行Git库扩展。

后来，Twitter引入去中心化的方案，在程序更新中采用BitTorrent，将更新时间从40分钟降低到12秒！


Capistrano： Ruby世界流行的一个部署框架，在实际服务器上执行任务。
Git：代码管理。
Murder：启动BTtracker，创建seeds，文件传输等任务。
BitTornado：对BitTorrent改造，更适合DataCenter内部高质量的网络环境。




# Murder: Fast datacenter code deploys using       BitTorrent







## Thursday, July 15, 2010







Twitter has thousands of servers. What makes having           boatloads of servers particularly annoying though is that we           need to quickly get multiple iterations of code and binaries           onto all of them on a regular basis. We used to have a           git-based deploy system where we’d just instruct our           front-ends to download the latest code from our main git           machine and serve that. Unfortunately, once we got past a few           hundred servers, things got ugly. We recognized that this           problem was not unlike many of the scaling problems we’ve had           and dealt with in the past though -- we were suffering the           symptoms of a centralized system.




By sitting beside a particularly vocal Release Engineer, I           received first-hand experience of the frustration caused by           slow deploys. We needed a way to dramatically speed things up.           I thought of some quick hacks to get this fixed: maybe           replicate the git repo or maybe shard it so everyone isn’t           hitting the same thing at once. Most of these           quasi-centralized solutions will still require re-replicating           or re-sharding again in the near future though (especially at           our growth). It was time for something completely different,           something decentralized, something more like.. [BitTorrent](http://bittorrent.com/).. running           inside of our datacenter to quickly copy files around. Using           the file-sharing protocol, we launched a side-project called           Murder and after a few days (and especially nights) of nervous           full-site tinkering, it turned a 40 minute deploy process into           one that lasted just 12 seconds!




Murder (which by the way is the name for a [flock             of crows](http://en.wikipedia.org/wiki/List_of_collective_nouns_for_birds)) is a combination of scripts written in Python           and Ruby to easily deploy large binaries throughout your           company’s datacenter(s). It takes advantage of the fact that           the environment in a datacenter is somewhat different from           regular internet connections: low-latency access to servers,           high bandwidth, no NAT/Firewall issues, no ISP traffic           shaping, only trusted peers, etc. This let us come up with a           list of optimizations on top of BitTornado to make BitTorrent           not only reasonable, but also effective on our internal           network. Since at the time we used Capistrano for signaling           our servers to perform tasks, Murder also includes a           Capistrano deploy strategy to make it easy for existing users           of Capistrano to convert their file distribution to be           decentralized. The final component is the work Matt Freels (@[mf](http://twitter.com/mf)) did in bundling           everything into an easy to install ruby gem. This further           helped get Murder to be usable for more deploy tasks at           Twitter.










Reference：
[http://engineering.twitter.com/2010/07/murder-fast-datacenter-code-deploys.html](http://engineering.twitter.com/2010/07/murder-fast-datacenter-code-deploys.html)
Murder：（Python/Ruby）[http://github.com/lg/murder](http://github.com/lg/murder)
