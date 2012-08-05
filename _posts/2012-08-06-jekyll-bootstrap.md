---
layout: post
title: "切换到jekyll bootstrap"
description: ""
category: 
tags: []
---
{% include JB/setup %}

把博客程序切换的[jekyll-bootstrap](http://jekyllbootstrap.com)。切换的原因是看中了它的几个优点：

1. 有命令行工具。可以通过rake post命令创建日志。
2. 支持插件。可以方便利用别人制作的插件，这点很重要。
3. 可以定义页面风格。
4. 从头开始学习jekyll太耗费时间和精力。

切换的过程比较顺利，执行git merge，只有6个文件需要手工合并，而且没有严重冲突。合并完毕将代码提交到github即可。
