---
layout: post
title: "Graphite安装笔记"
description: ""
categories: [Technology]
tags: [Graphite]
---
{% include JB/setup %}


graphite是个不错的监控系统，不过文档真的很烂。之前 @ARGV 写过一篇[blog](http://chenlinux.com/2013/04/03/install-graphite/)介绍了debian系统上的安装过程。

由于安装过程在最新版有一些变化，所以将我最近手动安装的过程整理出来，希望对你有帮助。

整个过程采用的系统是CentOS 6.4 x86_64。软件仓库配置了EPEL。





# 安装过程记录

## yum安装

其实[EPEL仓库](https://fedoraproject.org/wiki/EPEL)中已经有graphite的安装包。设置EPEL仓库，用yum直接安装即可。

    yum --enablerepo=epel install graphite-web python-carbon -y


## 手工安装

### 准备工作目录

    mkdir -p ~/graphite_setup && cd ~/graphite_setup


### 安装whisper

    git clone https://github.com/graphite-project/whisper.git
    cd whisper && sudo python setup.py install && cd -


### 安装carbon

    git clone https://github.com/graphite-project/carbon.git
	cd carbon && python setup.py install && cd -
	cd /opt/graphite/conf
	cp carbon.conf.example carbon.conf 
	cp storage-schemas.conf.example storage-schemas.conf
根据需求调整carbon.conf中的配置。


### 安装graphite-web

    git clone https://github.com/graphite-project/graphite-web.git
	cd graphite-web && python check-dependencies.py
	 
	#如果报告依赖问题，需要安装对应的软件包
	yum install pycairo django pytz pyparsing django-tagging python-memcached python-ldap twisted txamqp python-rrdtool -y
	
	sudo python setup.py install 

	cd /opt/graphite/webapp/graphite/
	cp local_settings.py.example local_settings.py

务必重新设置SECRET_KEY，根据需要调整TIME_ZONE和其它参数。

	PYTHONPATH=/opt/graphite/webapp/ django-admin syncdb --settings=graphite.settings


### 制造一些测试数据

    cd /opt/graphite/examples/
	python example-client.py

或者用脚本模拟也写随机数据

    ruby -e 'puts "test.op.random_seq #{rand(100)} #{Time.now.to_i}"' | nc 127.0.0.1 2003

### 启动web 服务

	cp /opt/graphite/examples/example-graphite-vhost.conf /etc/httpd/conf.d/
	service httpd restart


## 安装文档参考

	# 安装文档的更新 https://github.com/graphite-project/graphite-web/blob/master/INSTALL
	# https://github.com/graphite-project/graphite-web
