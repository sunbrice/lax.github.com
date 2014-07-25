---
layout: post
title: "Ruby-1.9.1之后版本支持kerberos问题的解决思路"
description: ""
categories: [Ruby]
tags: []
---
{% include JB/setup %}

net-ssh是ruby平台上常用的一个库，用于远程登录服务器。在支持kerberos的系统中，可以使用net-ssh-kerberos这个第三方库来支持实现。
随着近几年Ruby版本由1.8.x系列升级到1.9.x系列以及2.x系列，一系列兼容问题出现。

关于具体原因的分析和部分解决方法如下。

原因分析：
net-ssh-kerberos内部使用DL来调用kerberos库。DL接口是Ruby-1.8.7以及之前版本中用来调用动态链接库的方法，而在Ruby-1.9.1之后这一接口被废弃——对于一门成熟的语言，这一接口改动相对较大，直接导致很多第三方库无法工作，net-ssh-kerberos也包含在内。


临时解决办法是改用基于gssapi包装的net-ssh-krb。gssapi库采用了ffi方式，不受ruby版本升级到1.9.1之后的影响。
具体代码示例：

修改Gemfile

    # Gemfile, add following lines.
    gem 'net-ssh', :require => 'net/ssh'
	
    # for ruby <~ 1.8.7
    # gem 'net-ssh-kerberos', :git => 'https://github.com/Lax/net-ssh-kerberos.git', :branch => 'master', :require => 'net/ssh/kerberos'
    
    # for ruby ~> 1.9.1
    gem 'net-ssh-krb', :git => 'https://github.com/Lax/net-ssh-kerberos.git', :branch => 'gssapi', :require => 'net/ssh/kerberos'
	


执行

    $ bundle install
	


程序代码

    #!/usr/bin/env ruby
    require 'rubygems'
    require 'bundler'
    Bundler.require
    
	host='10.0.0.1'
	username='root'
	
    Net::SSH.start(host, username, {:auth_methods => ["gssapi-with-mic"]}) do |ssh|
      puts ssh.exec!('hostname')
    end



目前net-ssh-krb的开发版已经能够兼容Linux系统，而MacOSX系统上使用这个库还有一些问题。


项目说明文档：
http://blog.liulantao.com/net-ssh-kerberos/
