---
layout: post
title: "使用HAML开发页面"
description: ""
categories: []
tags: []
---
{% include JB/setup %}

![](http://haml.info/images/img-hero-boat.png)

[HAML](http://haml.info) 是一种简单的html模版语言。它的最大特点是短小精干整洁。

## 与erb的对比



**对比项目**|**ERB**|**HAML**
------------|---------|----------:
代码块      |需要关闭 |不需要关闭
逻辑层级    |与ruby语法相同|按空格缩进
变量        |ruby变量 |ruby变量



## 示例haml文件

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

## 示例html文件

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
