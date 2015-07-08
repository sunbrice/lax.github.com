---
layout: post
title: "使用软件包神器fpm将Python包转为RPM包"
date: 2014-05-08 11:56:15 +0800
comments: true
categories: Technology
commentIssueId: 2
---

在[之前的一篇文章](http://mib.cc/blog/2012/2012-05-21-package-management-with-fpm.html)中介绍过使用fpm制作rpm包，相信实践过的同学已经见识过fpm的威力。

作为软件包管理工具，fpm还可以实现不同软件包类型之间的相互转换。本文将简单演示一下软件包转换的功能。

文中用例来自于日常工作中的实际需求，需要在系统中安装Scrapy工具。写本文时scrapy的最新版本为0.22。不过业务指定的版本为0.16。

下面我们看一下软件包准备的过程。


## 转换第一个python包

首先制作python-scrapy包。

    fpm -s python -t rpm scrapy==0.16.5

说明：这里我们用到了-s参数，指定源格式。指定为python包时，fpm将使用easy_install的python源获取源文件。通过“==”来指定scrapy版本号，这与easy_install的写法完全一致。

命令执行完毕，可以看到当前目录生成文件```python-scrapy-0.16.5-1.noarch.rpm```。


## 解决依赖

使用yum命令测试安装。

    yum localinstall python-scrapy-0.16.5-1.noarch.rpm

从输出看出缺少依赖的```python-w3lib```包。

    Error: Package: python-scrapy-0.16.5-1.noarch (/python-scrapy-0.16.5-1.noarch)
           Requires: python-w3lib >= 1.2

使用同样的方式创建这个包。

    fpm -s python -t rpm w3lib==1.2


## "影子"包

继续使用yum命令测试安装，发现另一个依赖（```python-pyopenssl```）。

    yum localinstall python-scrapy-0.16.5-1.noarch.rpm python-w3lib-1.2-1.noarch.rpm

    Error: Package: python-scrapy-0.16.5-1.noarch (/python-scrapy-0.16.5-1.noarch)
           Requires: python-pyopenssl

在yum仓库base中，可以搜索到```pyOpenSSL.x86_64```这个包，因此可以利用已有的包，避免重复创建以及可能的文件冲突。这里创建一个叫做```python-pyopenssl```的“影子”包，通过依赖包的方式引入pyOpenSSL。

    fpm -s empty -t rpm -n python-pyopenssl -v 0.10 -d 'pyOpenSSL >= 0.10'

创建的方式与上面的有些差异。最主要的一项，将源格式指定为```empty```，意味着这个不包含文件。通过```-d```参数指定将引入的依赖包名及版本。这个rpm仅仅表示一个依赖关系。


现在重新测试一下

    yum localinstall python-scrapy-0.16.5-1.noarch.rpm python-w3lib-1.2-1.noarch.rpm python-pyopenssl-0.10-1.x86_64.rpm

从输出可以看到已经能完成依赖检查，引入了pyOpenSSL包，可以进行安装。
将生成的3个rpm包放入yum仓库，方便部署系统使用。


## 总结

本文以python包转换为rpm的例子，简要演示了fpm进行package格式转换的功能。同时兼顾利用“影子”包的方式来解决仓库中已有软件包但是不同名的问题。
