---
layout: post
name: package-management-with-fpm
title: 使用fpm管理软件打包
time: 2012-05-21 16:15:00 +08:00
categories: [Technology, Ruby]
tags:
- fpm
- rpm
---
[fpm](https://github.com/jordansissel/fpm)是一款支持多平台的打包管理工具。支持的平台包括：rpm, deb, tar, solaris等。

fpm的目标：

1.   无痛创建软件包

2.   修改已有的软件包

3.   抽取已有软件包中的脚本

## 安装
可以通过gem直接安装

    gem install fpm

如果失败，需要确保rubygems和ruby-devel已经安装

    # yum install rubygems ruby-devel -y

## 一个简单示例：打包squid

我们的squid安装在/opt/squid/目录下。

    fpm -s dir -t rpm -n squid -v 0.1 --post-install /opt/squid/bin/initVar.sh /opt/squid/

运行后会在当前目录生成squid-0.1-1.rpm。

在创建rpm文件时，我们增加了一个参数--post-install，指定了当安装完成时执行的脚本，可以利用这个功能来完成软件初始化工作。类似的参数还有--pre-install/--post-uninstall/--pre-uninstall等。
