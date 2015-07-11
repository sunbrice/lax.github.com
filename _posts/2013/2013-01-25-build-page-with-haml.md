---
layout: post
title: "使用HAML构建WEB页面"
description: ""
categories: [Ruby]
tags: []
---

![Haml boat](http://haml.info/images/img-hero-boat.png)  


## 前言

在日常系统运维工作中，经常需要为各种系统构建页面，提炼常用的操作放到***仪表盘***，以提高日常工作效率。

在WEB逻辑层的选择，Ruby中有便捷优雅的Sinatra，Perl中有Dancer，Python有web.py。

页面展现层的开发主要考虑两个方面  

1.   构建页面框架
     
     这个方面考虑构建页面的快速和可重复性。如果仍停留在手工写html页面的阶段，那就太落伍啦。
     
     常用的方案是基于模版生成HTML代码。PHP本身就是一种模版。
     
     另外还有ERB，Markdown等轻量级的标记语言，由于Markdown对于语法变量支持不容易集成到动态页面，因此主要用于静态页面构建。
     
2.   页面样式和交互管理
     
     jQuery无疑是交互管理的首选。如果有较多的数据图表展现，可以选用DDD等框架。
     
     页面样式方面，可以参考[jQuery UI](http://jqueryui.com), [YUI](http://yuilibrary.com)。
     
     从易用的角度，jQuery结合[Twitter Bootstrap](http://twitter.github.com/bootstrap/)是个好选择。


本文主要介绍其中```构建页面框架```的方案选择，即用HAML替代ERB。  


## haml与erb的对比

[HAML](http://haml.info) 是一种简单的html模版语言。它的最大特点是短小精干整洁。  

在HAML项目首页有一项简单的对比，在此引用一下

使用ERB生成的页面代码


    <section class=”container”>
      <h1><%= post.title %></h1>
      <h2><%= post.subtitle %></h2>
      <div class=”content”>
        <%= post.content %>
      </div>
    </section>


使用HAML生成的页面代码


    %section.container
      %h1= post.title
      %h2= post.subtitle
      .content
        = post.content


通过对比可以看出一些特点


**对比项目**|**ERB**  |**HAML**
------------|---------|----------:
代码行数    |7        |5
关闭标签    |需要关闭 |不需要关闭
逻辑层级    |与ruby语法相同|按空格缩进
变量        |ruby变量 |ruby变量


其中haml**不需要关闭**和**按空格缩进**两项特性，与Python的风格有几分相似（ruby代码中满屏end的现象，一直受到诟病。python用强迫症式的方案解决了这个问题）——HTML也需要关闭标签。


## 实际项目中的应用

以下示例以HAML构建了一个简单的导航页面

### 示例haml文件

    -# coding: UTF-8
    !!!
    %html(xml:lang='zh-cn' lang='zh-cn' xmlns='http://www.w3.org/1999/xhtml')
      %head
        %meta(content='text/html;charset=UTF-8' http-equiv='content-type')
        %link{:href => "/css/bootstrap.min.css", :rel =>"stylesheet", :type => "text/css", :media => "screen" }
      %body
        .container
          %h1 支持系统
          %ul.inline
            %li
              %a.btn{:href => '#'} Wiki
            %li
              %a.btn{:href => '#'} Tracker
            %li
              %a.btn{:href => '/ganglia'} Ganglia
            %li
              %a.btn{:href => '/nagios/'} Nagios
    
          %h3 查询
          %form{:action => "/tools/?", :method => "GET", :name => "dnsinfo_form"}
            %input#suggest_dns{:type => "text", :size => 25, :name => "q", :onmouseover => "this.focus()", :onfocus => "this.select()", :title => "关键字: 域名、IP、域名列表或IP列表(,号或空格分隔)", :autocomplete => "off", :class => "ac_input"}
            %input{:type => "submit", :value => "查询"}


### 示例html文件

使用haml命令生成文件

    haml index.haml > index.html

文件内容

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html lang='zh-cn' xml:lang='zh-cn' xmlns='http://www.w3.org/1999/xhtml'>
      <head>
        <meta content='text/html;charset=UTF-8' http-equiv='content-type' />
        <link href='/css/bootstrap.min.css' media='screen' rel='stylesheet' type='text/css' />
      </head>
      <body>
        <div class='container'>
          <h1>支持系统</h1>
          <ul class='inline'>
            <li>
              <a class='btn' href='#'>Wiki</a>
            </li>
            <li>
              <a class='btn' href='#'>Tracker</a>
            </li>
            <li>
              <a class='btn' href='/ganglia'>Ganglia</a>
            </li>
            <li>
              <a class='btn' href='/nagios/'>Nagios</a>
            </li>
          </ul>
          <h3>查询</h3>
          <form action='/tools/?' method='GET' name='dnsinfo_form'>
            <input autocomplete='off' class='ac_input' id='suggest_dns' name='q' onfocus='this.select()' onmouseover='this.focus()' size='25' title='关键字: 域名、IP、域名列表或IP列表(,号或空格分隔)' type='text' />
            <input type='submit' value='查询' />
          </form>
        </div>
      </body>
    </html>
