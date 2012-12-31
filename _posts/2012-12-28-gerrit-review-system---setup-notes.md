---
layout: post
title: "Gerrit Review System - Setup notes"
description: ""
categories: [Technology]
tags: [ Gerrit, Git, Code Review ]
---
{% include JB/setup %}

* toc
{:toc}

## Gerrit背景

Android作为移动平台上兴起的大型开源操作系统项目，管理大量代码提交者的代码是个复杂的工程。我们来看一下Android项目使用的协作平台Gerrit。

Gerrit是Andriod工程中发展起来的源代码review工具，基于[git](http://www.kernel.org/pub/software/scm/git/)，利用了git的branch和merge机制。已经有很多著名的开源项目在使用这个工具来协调代码提交流程，比如Eclipse，CloudFoundry，OpenStack，LibreOffice等开源项目。

## 协作流程简介
在Gerrit的流程中，代码提交经过以下步骤

*    Push ：代码提交
*    Code Review ： 看代码，确定是否合理
*    Verify ： 应用代码，确认是否能正确执行
*    Submit Patch ： 与已发布版本合并，成功后合并到master分支
*    Rollback ：以上每个步骤都可能失败，并进行相应的流程回退

从Android项目的代码提交流程图，可以看到开发者提供一个patch的生命周期。虽然看起来有些复杂，但是这些步骤都在一个web界面中完成。

<img src='http://source.android.com/images/workflow-0.png' />

## 自定义Gerrit安装

可喜的是我们能够应该gerrit在自有的管理流程中，按照文档可快速把gerrit搭建起来。gerrit源码中自带的安装文档中有详细的安装过程和注意事项[Documentation/install.txt](http://code.google.com/p/gerrit/source/browse/Documentation/install.txt)

身份验证方面，默认支持使用OpenID，HTTP或者LDAP验证。对于其它需要比如Kerberos，需要进行一些特殊调整，这在文档中还没有完整说明。

比如在Kerberos方式身份验证部署时，部署httpd作为反向代理，同时采用httpd内置的kerberos模块进行身份验证。配置httpd可以将kerberos登录后的username通过一个http头传给后端的gerrit服务器，实现免密码登陆。关于httpd的配置可以参考[这个文档](http://www.mailinglistarchive.com/html/repo-discuss@googlegroups.com/2012-01/msg00168.html)。注意需要配置keytab文件，以及传递用户名的Header名。


数据库选择方面，默认使用内置的H2快速搭建。对于希望长期使用并扩展的系统，应该选择MySQL或PostgreSQL。安装文档中有详细操作说明。


从[google code](http://code.google.com/p/gerrit/downloads/list)下载最新版本war包，安装java环境，根据提示设置数据库。身份验证方式选择HTTP（与上文提到Kerberos有关，根据情况可选择OpenID和LDAP等）。


配置反向代理时，登陆页面/login/会跳转到/login/null#null并死循环。在配置文件gerrit.config设置canonicalWebUrl选项可以解决。

    [gerrit]
    	canonicalWebUrl = http://<address of httpd>/review/

邮件功能可以选择关闭，在gerrit.config将sendemail设置enabled=false即可关闭。关闭后不能使用邮件功能，包括用户注册的确认邮件。

    [sendemail]
    	enabled = false

代码提交步骤依赖用户注册时填写的email地址。关闭sendemail功能时，需要在数据库中直接修改accounts中用户的preferred_email字段。

## 创建新项目

在Gerrit页面上Projects项目下，可以创建项目。安装后默认已有一个叫做All Projects的项目，新项目默认继承这个项目的属性。

## 应用gerrit进行代码管理

gerrit服务端部署成功后，客户端的部署相对简单很多。推荐使用gerrit-cli工具。

gerrit-cli安装方法：

    yum install ruby rubygems -y
    gem install gerrit-cli

安装完毕后即可使用gerrit命令。

    cd /tmp/
    gerrit clone ssh://<username>@<ip address>:29418/<repo name>.git
    # cd repo dir && make changes
    git commit -a
    gerrit push

执行push命令成功后，即可在gerrit页面上看到提交的代码开始了review流程。

## 常见问题处理

### Forge Committer Identity

使用gerrit push命令提交修改时，如果提交历史中有其它人的commit（mirror项目代码时一般是这种情况），会提示email不一致。设置Forge Committer Identity权限可以解决。

操作方法：Projects -> List -> All-Projects -> Access，打开权限编辑页面，点Edit按钮。
在Reference: refs/*部分，Add Permission -> Forge Commiter Identity，Allow -> Registered Users。保存后即可正常提交。

