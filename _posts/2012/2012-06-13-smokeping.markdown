---
layout: post
name: network-monitor-with-smokeping
title: 使用smokeping监控网络质量
time: 2012-06-13 17:50:00 +08:00
categories: [SysAdmin]
tags:
- smokeping
- rrdtool
---
[smokeping](http://oss.oetiker.ch/smokeping)是一款网络质量检测软件，用perl写成，使用rrdtool保存数据。

它的优势是轻量，易于安装，可定制性强。

### 安装过程

下载安装包：[下载页面](http://oss.oetiker.ch/smokeping/pub/)

写作此文时最新版本为2.6.8。

解压后进入目录，执行命令

    ./setup/build-perl-modules.sh /opt/smokeping-2.6.8/thirdparty

    ./configure

如果提示有模块安装失败，可以从CPAN安装。

    perl -MCPAN -e 'install FCGI'

确保configure成功后，执行

    qmake install

创建必要的目录

    cd /opt/smokeping-2.6.8/ && mkdir cache var data

安装rrdtool，httpd和mod_fcgid

    rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm

    yum install rrdtool httpd mod_fcgid -y

配置httpd。创建配置文件/etc/httpd/conf.d/smokeping.conf，内容如下

    alias	/smokeping/cache	/opt/smokeping-2.6.8/cache/
    alias	/smokeping	/opt/smokeping-2.6.8/htdocs/

    <Directory /opt/smokeping-2.6.8/cache/>
        Order allow,deny
        Allow from all
    </Directory>
    <Directory /opt/smokeping-2.6.8/htdocs/>
        #SetHandler fcgid-script
        Options +ExecCGI

        Order allow,deny
        Allow from all
    </Directory>

修复cache目录权限，否则查看页面会报错。

    chown apache /opt/smokeping-2.6.8/cache/

启动smokeping

    [root@lax-iPad smokeping-2.6.8]# ./bin/smokeping
    Note: logging to syslog as local0/info.
    Daemonizing ./bin/smokeping ...

启动httpd

    # service httpd start

在浏览器中查看

    http://localhost/smokeping/smokeping.fcgi.dist
