---
date: '2011-01-19 10:06:17'
layout: post
slug: gentoo-on-thinkpa-x201i-kernel-config-to-support-trackpoint-and-touchpad
status: publish
title: Gentoo on Thinkpad X201i, kernel config to support trackpoint and touchpad.
wordpress_id: '35'
categories:
- Technology
tags:
- kernel
- Linux
- synaptics
- thinkpad
- trackpoint
- x201i
---

前段时间一直在xorg.conf中想办法支持小红点，未果。昨晚在内核配置选项中随手搜索了一下，发现有一个PS2驱动是可以支持小红点的。于是知道原来的方向就是错的。

CONFIG_MOUSE_PS2=y
CONFIG_MOUSE_PS2_SYNAPTICS=y
CONFIG_MOUSE_PS2_TRACKPOINT=y

重新配置后再编译一下内核，小红点的生效了。

顺便把synaptics驱动也装上了。感觉x201i的12寸身躯，配上一个小尺寸的触控板是完全多余，触控板要很大尺寸，用起来才爽，在此羡慕mackook一下。
