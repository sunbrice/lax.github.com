---
layout: post
title: "查看github pages中文档的方式"
description: ""
categories: []
tags: [github, rvm, jekyll]
---

## 背景

由于[抢票插件事件](https://github.com/iccfish/12306_ticket_helper/issues/16)，近期github pages不能正常访问。作为一个普通码农，严重依赖部署在github的项目，这些项目也几乎都以github pages提供文档。缺乏文档确实很不方便，各地的有证与无证码农们已经在weibo等渠道表达不满。

抱怨归抱怨，仍然要想办法看到这些文档，为周末生活找点儿乐趣——**尤其今天这雪后的雾霾天气**。

## 方法

Github pages使用的是一套基于Jekyll的模版系统，原则上只要把各项目的文档源码checkout到本地，重新生成一遍即可。幸运的是这些项目仍可以访问。

以bootstrap库为例来说明一下。

首先获取代码到本地

    git clone git://github.com/twitter/bootstrap.git

进入代码目录，切换到gh-pages分支

    cd bootstrap
    git checkout gh-pages

启动jekyll本地服务器

    jekyll --server

在浏览器查看

    open http://127.0.0.1:4000


jekyll是一个ruby工具，你需要先安装ruby和rubygems。

安装RVM:

    curl -L https://get.rvm.io | bash -s stable

安装Jekyll:

    gem install jekyll
