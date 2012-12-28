---
layout: post
title: "Gerrit Review System - Setup notes"
description: ""
categories: [Technology]
tags: [ Gerrit, Git, Code Review ]
---
{% include JB/setup %}

跟随gerrit源码中自带的安装文档中的[Documentation/install.txt](http://code.google.com/p/gerrit/source/browse/Documentation/install.txt)

从google code下载了war包，安装过程根据文档即可完成，需要根据提示设置数据库。身份验证方式选择HTTP（与下文Kerberos有关）。

由于我们使用Kerberos，部署时，在gerrit前端部署了httpd作为身份验证。httpd将kerberos登录后的username通过一个http头传给后端的gerrit服务器。

关于httpd的配置可以参考[这个邮件](http://www.mailinglistarchive.com/html/repo-discuss@googlegroups.com/2012-01/msg00168.html)。注意需要配置keytab文件。

配置后遇到的第一个问题是在登陆页面/login/出错。遇到的情形是跳转到/login/null#null后死循环，浏览器报错。在配置文件gerrit.config加入下面以后即可。

    [gerrit]
    	canonicalWebUrl = http://git.d.xiaonei.com/review/

另一个问题是用户的email。使用gerrit push命令提交修改时，一直提示email不一致的错误。可以通过提升权限来处理。操作方法：Projects -> List -> All-Projects -> Access，打开权限编辑页面，点Edit按钮。
在Reference: refs/*部分，Add Permission -> Forge Commiter Identity，Allow -> Registered Users。保存后即可正常提交。

由于没配置邮件代理，所以在gerrit.config将sendemail关闭。

    [sendemail]
    	enabled = false

