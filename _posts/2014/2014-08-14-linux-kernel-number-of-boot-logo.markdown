---
layout: post
title: "【八卦】关于Boot LOGO中企鹅数量与CPU核心数的关系"
date: 2014-08-14 17:41:22 +0800
comments: true
categories: Technology
tags: [Linux, Kernel, Boot Logo, Gentoo]
---

前段时间在<b> <s>工作</s> 玩乐 </b>用的 [MacBook Pro 上安装 Gentoo](https://github.com/Lax/MacBook-Gentoo-Notes) ，这本是一件枯燥的事情。由于启动时遇到内核 Panic 的问题，顺手在微博上晒图。海聊中正好 [@喝雪碧的虾PeterCxy](http://weibo.com/u/1789004515) 提到 Boot Logo 里小企鹅的个数问题。

Boot Logo 是什么？ 系统启动时在启动信息上方显示的图形，需要支持 fbdev 才能显示。本文最后附图中那几只可爱的小企鹅就是。

对于企鹅数量与 CPU 核心数的对应关系，当时只有一点点零星印象，随手 Google 想验证一下也没有结果。为了保持谦虚谨慎靠谱的形象，没有继续扯下去。

![](http://ww2.sinaimg.cn/mw690/62909bbcjw1ejcc1fw4nxj20fs0bh75e.jpg)


今天下午想到这个问题，顺手翻了一下 fbdev 相关的代码，终于找到了出处作为依据。话不多说了，上代码。

{% highlight c %} # drivers/video/fbdev/core/fbmem.c

int fb_show_logo(struct fb_info *info, int rotate)
{
    int y;

    y = fb_show_logo_line(info, rotate, fb_logo.logo, 0,
    num_online_cpus());
    y = fb_show_extra_logos(info, y, rotate);

    return y;
}
{% endhighlight %}

相关代码可以查看这里：
[http://lxr.free-electrons.com/source/drivers/video/fbdev/core/fbmem.c#L663](http://lxr.free-electrons.com/source/drivers/video/fbdev/core/fbmem.c#L663)


![](http://ww3.sinaimg.cn/bmiddle/62909bbcjw1eizstx1qykj20p018gwm9.jpg)
