---
date: '2010-12-12 11:18:58'
layout: post
slug: colinux-on-windows-7-as-system-service
status: publish
title: coLinux on windows 7 as system service
wordpress_id: '32'
categories:
- Technology
tags:
- colinux
- windows7
---

在windows 7系统上，跑一个virtual box虚拟Linux有点儿累人。其一，每次都要启动virtual box管理器，再启动指定的虚拟机；其二，任务栏多出两个不怎么常用的东西；其三，那个vb的程序性能也不是太爽。

由于cygwin等方案不能模拟原生的linux二进制程序，因此需要一个改进效率的虚拟化方案。

coLinux（ Cooperative Linux：http://www.colinux.org/ ）是一种比较理想的解决方案。coLinux提供一个兼容的内核，能够让linux系统高效的运行在windows系统之上。

colinux的安装非常方便，下载安装包直接运行即可。安装过程会提示下载system image，安装网卡驱动程序等。安装后需要修改配置文件，以便启动特定的系统。

一个典型的虚拟系统包括一个.bat格式的启动文件，一个.conf格式的配置文件，以及一个磁盘镜像文件。主要的配置工作需要在配置文件中完成。

colinux使用了tap设备，网络配置在虚拟机中都是透明的。

kernel=vmlinux

cobd0="Debian-5.0r2-lenny.ext3.2gb"

#cobd1="swap.disk"

cofs0=D:coLinuxShare

root=/dev/cobd0

# Additional kernel parameters (ro = rootfs mount read only)
#ro

initrd=initrd.gz

#mem=256

cocon=120x40

eth0=slirp

eth1=tuntap,colinux-tap
