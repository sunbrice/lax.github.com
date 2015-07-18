---
layout: post
name: gnome-shell-xinerama
title: GNOME3多显示器配置
time: 2012-06-19 13:50:00 +08:00
categories: [SysAdmin]
tags:
- gnome-shell
- xinerama
---
gnome3多显示器默认只有mirror模式工作正常。使用扩展模式，则导致整个屏幕横跨两个显示器，工具条和窗口被拆分。

经检查资料，发现需要配置USE flag： xinerama。

    # File: /etc/make.conf

    USE="$USE xinerama"

修改后需要重新emerge一下已安装的软件包，之后重启X

[Xinerama](http://www.gentoo-wiki.info/HOWTO_Dual_Monitors#Using_Xinerama)
