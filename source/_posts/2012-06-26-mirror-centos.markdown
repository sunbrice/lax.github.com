---
layout: post
name: mirror-centos
title: CentOS镜像攻略
time: 2012-06-26 20:28:00 +08:00
categories: [SysAdmin]
tags:
- CentOS
- mirror
- Shell
---
这篇文章讲述了通过rsync协议来进行CentOS软件仓库镜像的技术，如果你觉得实用我的目的就达到了。

先从[这个页面](http://www.centos.org/modules/tinycontent/index.php?id=30)找到一个支持rsync协议的镜像站点，我选择了[Linux Kernel Archives](http://www.kernel.org/)，当然你可以根据情况选择其它站点。

之后是准备磁盘空间，创建目录。

    mkdir -p /data/mirrors/centos/{5.8,6.2}

接着就可以开始进行同步。

    rsync  -avSHP --delete --exclude "local*" --exclude "isos" rsync://mirrors.kernel.org/centos/5.8/ /data/mirrors/centos/5.8/

    rsync  -avSHP --delete --exclude "local*" --exclude "isos" rsync://mirrors.kernel.org/centos/6.2/ /data/mirrors/centos/6.2/

CentOS的版本号是由一个主版本号和一个次版本号组成。实际使用yum访问软件仓库时，一般根据主版本号来查找各自的目录，比如CentOS 5.8的系统通过5而不是5.8来查找仓库的目录。因此需要为这两个主版本号创建软连接。

    cd /data/mirrors/centos/ && ln -s 5.8 5 && ln -s 6.2 6

之后是修改yum的配置文件，即/etc/yum.repos.d/CentOS-Base.repo。下面给出base仓库的示例。类似可以配置
- addons
- centosplus
- contrib
- cr
- extras
- fasttrack
- os
- updates

    [base]
    name=CentOS-$releasever - Base
    #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
    baseurl=http://mirrors.renren.com/centos/$releasever/os/$basearch/
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

为了方便部署，我写了一个更强壮的脚本，[点击获取](https://raw.github.com/Lax/SA/master/centos/rsync-centos)。通过为该脚本创建软连接，可以在不传参数的情况下更新不同版本的仓库目录。软连接的格式有要求，需要是rsync-centos-VER（VER是完整版本号）

    ln -s rsync-centos rsync-centos-5.8

    ln -s rsync-centos rsync-centos-6.2

放入计划任务中每天凌晨执行（对于国外的服务器，北京时间的凌晨执行并不是一个好主意）

    0 2 * * *  cd /path/to/scripts/ && sh rsync-centos-6.2 && rsync-centos-5.8

