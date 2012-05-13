---
date: '2011-09-28 09:11:39'
layout: post
slug: build-rack-based-application-on-heroku
status: publish
title: Build Rack-based application on Heroku
wordpress_id: '76'
categories:
- Technology
tags:
- DataMapper
- git
- heroku
- PaaS
- rack
- ruby
---

## heroku


heroku是目前最方便使用的PaaS系统，是大部分开发者部署ruby服务的首选，基于git的部署工具带给它比google appengine更好的便捷性。

在heroku创建一个rails程序是非常简单的，基本步骤（只是基本步骤，具体命令需要查rails和git的使用说明）：
1、rails create myapp
2、heroku create
3、cd myapp && git init .
4、git commit && git push

搞出这样的开发流程，是造福广大程序员的伟大事业。




## Why Rack + DataMapper on heroku


Rails + ActiveRecord可以算作一种标配，引领了Ruby开发的潮流。这次尝试Ruby off <del>Rails</del>，选用了Rack + DataMapper的组合。

大部分ruby web框架都支持rack规范，如主流的rails、sinatra、merb。rack定义了一系列规范，类似java的servlet。遵循规范的结果，使得大量rack容器被开发出来。heroku目前默认的容器是thin。

从heroku的文档中看出，heroku使用了postgresql数据库。对于大部分ruby程序而言，使用mysql或pgsql或sqlite都不会影响部署——因为引入数据库抽象层已成惯例，比如ActiveRecord、DataMapper和Sequeel等。




## First thing first： 部署一个简单的Rack程序


在heroku上部署基于Rack的程序，也是非常简单的。

最简单的config.ru内容如下


> map "/"

do    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, StringIO.new("sysadmin 0.1")] }

end


本地直接用rackup即可执行。

如果在heroku上部署，需要额外的Gemfile文件来指明服务器端需要的rubygem。最简单的Gemfile内容为：


> gem 'rack'


将这两个文件git push到heroku上去，可以通过myapp.heroku.com看到页面显示"sysadmin 0.1"。到此最简单的部署完成。




## Setup DataMapper




> require 'data_mapper'

DataMapper.setup(:default, ENV['DATABASE_URL'] || 'sqlite::memory:')

class Tweet

include DataMapper::Resource

property :id,   Serial

property :name, String

property :value, Text

property :created_at, DateTime

end
DataMapper.finalize
DataMapper.auto_upgrade!


经过这一操作，我们就有了一个class：Tweet，并且在数据库中有相应的表生成。一个基于DataMapper进行数据操作的应用就此诞生。
