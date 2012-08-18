---
layout: post
name: gentoo-portage-tree-sync-with-git
title: 使用git同步更新portage
time: 2012-05-20 21:45:44 +08:00
categories:
- Technology
tags:
- Gentoo
- Portage
- Git
- Funtoo
---
Gentoo的Portage默认使用rsync进行同步，网速不好时每次操作至少要几分钟的等待。幸好Gentoo开发者之一[Daniel Robbins](http://www.funtoo.org/wiki/User:Drobbins)维护了[Funtoo](http://www.funtoo.org)，使用github来分发portage树，并且有一个[Gentoo portage的快照](https://github.com/funtoo/portage)，每天同步两次。

使用起来很简单，只需简单的命令

    git clone git://github.com/funtoo/portage.git && cd portage && git checkout gentoo.org 

之后将取回的portage目录替换原来的/usr/portage/即可。

速度问题应该由来已久，不明白这群极客为什么不早些采用Git。

从Gentoo讨论区看到，开发者还没有将用户同步Portage的方式迁移到git的计划（开发者的Portage树由CVS迁移到Git的工作已经列入计划）。
