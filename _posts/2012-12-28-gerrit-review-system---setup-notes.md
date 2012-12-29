---
layout: post
title: "Gerrit Review System - Setup notes"
description: ""
categories: [Technology]
tags: [ Gerrit, Git, Code Review ]
---
{% include JB/setup %}

Gerrit是Andriod工程中发展起来的源代码review工具，有很多著名的开源项目在使用这个工具来协调代码提交流程。
目前有CloudFoundry、OpenStack，LibreOffice等开源项目采用Gerrit。

下面是Android项目的代码提交流程图，可以看到一个patch的生命周期。虽然看起来有些复杂，但是这些步骤都在一个web界面中完成。

<img src='http://source.android.com/images/workflow-0.png' />

代码提交经过以下步骤

*    代码提交
*    Code Review ： 看代码，确定是否合理
*    Verify ： 应用代码，确认是否能正确执行
*    Submit Patch ： 与已发布版本合并
*    以上每个步骤都可能失败，并进行相应的流程回退


可喜的是我们能够按照文档快速把gerrit搭建起来。gerrit源码中自带的安装文档中有详细的安装过程和注意事项[Documentation/install.txt](http://code.google.com/p/gerrit/source/browse/Documentation/install.txt)

由于我们使用Kerberos，部署时，在gerrit前端部署了httpd作为身份验证。httpd将kerberos登录后的username通过一个http头传给后端的gerrit服务器。

关于httpd的配置可以参考[这个邮件](http://www.mailinglistarchive.com/html/repo-discuss@googlegroups.com/2012-01/msg00168.html)。注意需要配置keytab文件，以及通过Header传递用户名。

数据库选择方面，可以使用内置的H2快速搭建，对于希望长期使用并扩展的系统，还是用MySQL或PostgreSQL比较合适。具体操作步骤和建表过程在安装文档中有详细说明。

从google code下载了war包，安装过程根据文档即可完成，需要根据提示设置数据库。身份验证方式选择HTTP（与Kerberos有关）。

配置后遇到的第一个问题是在登陆页面/login/出错。遇到的情形是跳转到/login/null#null后死循环，浏览器报错。在配置文件gerrit.config加入下面以后即可。

    [gerrit]
    	canonicalWebUrl = http://git.d.xiaonei.com/review/

由于没配置邮件代理，所以在gerrit.config将sendemail关闭。

    [sendemail]
    	enabled = false

代码提交步骤依赖用户注册时填写的email，而内部搭建时sendemail已关闭，无法发送确认邮件。一个妥协办法是在数据库中直接修改accounts中用户的preferred_email字段。

另一个问题是使用gerrit push命令提交修改时，如果提交历史中有其它人的commit（mirror项目代码时一般是这种情况），一直提示email不一致的错误。可以通过提升权限来处理。操作方法：Projects -> List -> All-Projects -> Access，打开权限编辑页面，点Edit按钮。
在Reference: refs/*部分，Add Permission -> Forge Commiter Identity，Allow -> Registered Users。保存后即可正常提交。


