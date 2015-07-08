---
layout: post
title: "使用InstantClick提升链接打开速度"
date: 2014-03-03 12:11:32 +0800
comments: true
categories: Technology

---

博客页面总是慢吞吞，这样的体验大概是每个博客作者都不愿意看到的。

最近有一个叫做InstantClick的项目，利用鼠标点击链接前的短暂时间进行预加载，从而在感观上实现了迅速打开页面的效果。

### InstantClick原理

#### What is it

InstantClick是一个能神奇的改善网站浏览速度的JavaScript库。


#### Why

尽管网络带宽已经有很大增加，网站并没有变得更快，这是因为加载网页时的最大平均是网络延时。而延时是一个不可避免的物理限制，因此InstantClick使用了预加载的方式来*取巧*达到加速目的。

在访问者点击一个链接之前，鼠标会悬停在链接上面。悬停(mouseover)或按下(mousedown)与点击(click,按下并松开鼠标)事件之间通常有200ms~300ms的间隔，InstantClick 利用这个时间间隔预加载页面。这样当你打开页面的时候，其实页面已经加载到本地了，也就会很快能个完成渲染。

###设置方法(以[octopress](http://octopress.org)为例)

#### 下载instantclick.js
在[InstantClick下载页面](http://instantclick.io/start.html)下载最小化的js文件，只有1.6kb。

url [http://instantclick.io/instantclick.min.js]

放到`octopress的_source/javascripts/`目录中
    
    curl http://instantclick.io/instantclick.min.js -o octopress的_source/javascripts/instantclick.min.js



#### 初始化

在布局文件载入js，并初始化。（本例通过whitelist模式初始化，既默认不对链接开启预加载，需要对每个链接指定，防止产生多余的请求）。

修改`source/_includes/custom/after_footer.html`

    <script src="{{ root_url }}/javascripts/instantclick.min.js"></script>
    <script data-no-instant>InstantClick.init(true);</script>


#### 针对每类链接开启预加载

`a`标签中增加data-instant属性，即可开启预加载。

如导航栏的链接：

    <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
    <li><a href="{{ root_url }}/about">About me</a></li>

修改为

    <li><a href="{{ root_url }}/blog/archives" data-instant>Archives</a></li>
    <li><a href="{{ root_url }}/about" data-instant>About me</a></li>

### 总结

单纯提高网络速度难以快速提升性能时，使用预加载提升体验已经是一个普遍采用的方式。

社交网站可以采用预加载技术将热门图片预先加载到用户本地，新闻网站可以预先读取新闻内容，通过这种方式来避免引起用户等待过久而砸显示器的冲动。
