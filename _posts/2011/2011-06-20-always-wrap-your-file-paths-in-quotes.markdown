---
date: '2011-06-20 14:44:58'
layout: post
slug: always-wrap-your-file-paths-in-quotes
status: publish
title: Always wrap your file paths in quotes
wordpress_id: '67'
categories:
- Technology
---

[Commit a047be85247755cdbe0acce6f1dafc8beb84f2ac to MrMEEE/bumblebee - GitHub](https://github.com/MrMEEE/bumblebee-Old-and-abbandoned/commit/a047be85247755cdbe0acce6#diff-1).

一个空格，导致血淋淋悲剧的发生，因引发了程序员们对于代码质量的反思。

一个小项目的安装文件，做了小小的bugfix，具体为：


> 

> 
> 

> 
> `@@ -348,7 +348,7 @@ case "$DISTRO" in`
> 
> 

> 
> `-  rm -rf /usr /lib/nvidia-current/xorg/xorg`
> 
> 

> 
> `+  rm -rf /usr/lib/nvidia-current/xorg/xorg`
> 
> 








关于code review，大家的评论热烈非常。






