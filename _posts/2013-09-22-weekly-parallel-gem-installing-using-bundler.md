---
layout: post
title: "[Weekly]Ruby2.1 GC改进;Bundler并行安装"
description: ""
categories: []
tags: []
---
{% include JB/setup %}

近期关注：

####   ruby 2.1将对GC进行改进
matz在baruco上宣布在ruby 2.1中将改进GC功能。这对于大规模部署ruby应用的企业来说，是个好消息。ruby官方版本（MRI）一直被人诟病，其中就包含GC方面的原因。而在此之前，大规模部署一般要借助于REE或者JRuby等第三方实现。

MRI和Rubinius的内存管理采用copy on write方式，亦即fork出的进程会与父进程共用一部分内存空间，除非对内存有改动。但是ruby进程进行GCd的方式是mark-and-sweep，导致对内存中的对象进行扫描及标记。首次进行GC时，copy on write的效果被抵消。

http://www.infoq.com/news/2013/09/ruby-2-1-gc-revamp

####   Bundler开始支持并行安装gems
从bundler~>1.4.0开始，Bundler将可以通过-jN选项来指定并行度，并发安装gems。经本人测试，对于标准的rails应用，初次bundle安装的速度提升接近一倍。

测试过程

    rvm gemset delete bundler-j1
    rvm gemset delete bundler-j8
    
    rvm gemset use bundler-j1 --create && gem install bundler --pre && cd /tmp/ && git clone https://github.com/rails/rails.git rails-j1 && cd rails-j1 && time bundle install -j1
    rvm gemset use bundler-j8 --create && gem install bundler --pre && cd /tmp/ && git clone https://github.com/rails/rails.git rails-j8 && cd rails-j8 && time bundle install -j8


结果(-j1):

    ➜  ~    rvm gemset use bundler-j1 --create && gem install bundler --pre && cd /tmp/ && git clone https://github.com/rails/rails.git rails-j1 && cd rails-j1 && time bundle install -j1
    Using ruby-head with gemset bundler-j1
    Fetching: bundler-1.4.0.pre.2.gem (100%)
    Successfully installed bundler-1.4.0.pre.2
    Parsing documentation for bundler-1.4.0.pre.2
    Installing ri documentation for bundler-1.4.0.pre.2
    1 gem installed
    Cloning into 'rails-j1'...
    remote: Finding bitmap roots...
    remote: Counting objects: 383508, done.
    remote: Compressing objects: 100% (103485/103485), done.
    remote: Total 383508 (delta 277740), reused 382388 (delta 276656)
    Receiving objects: 100% (383508/383508), 94.56 MiB | 518 KiB/s, done.
    Resolving deltas: 100% (277740/277740), done.
    Fetching gem metadata from https://rubygems.org/.........
    Fetching gem metadata from https://rubygems.org/..
    Resolving dependencies...
    Using rake (10.1.0)
    Installing i18n (0.6.5)
    Installing json (1.8.0)
    Installing minitest (5.0.8)
    Installing atomic (1.1.14)
    Installing thread_safe (0.1.3)
    Installing tzinfo (0.3.37)
    Using activesupport (4.1.0.beta) from source at .
    Installing rack (1.5.2)
    Installing rack-test (0.6.2)
    Using actionpack (4.1.0.beta) from source at .
    Installing builder (3.1.4)
    Using activemodel (4.1.0.beta) from source at .
    Installing erubis (2.7.0)
    Using actionview (4.1.0.beta) from source at .
    Installing mime-types (1.25)
    Installing polyglot (0.3.3)
    Installing treetop (1.4.15)
    Installing mail (2.5.4)
    Using actionmailer (4.1.0.beta) from source at .
    Installing arel (4.0.0)
    Using activerecord (4.1.0.beta) from source at .
    Installing bcrypt-ruby (3.1.2)
    Installing benchmark-ips (1.2.0)
    Using bundler (1.4.0.pre.2)
    Installing coffee-script-source (1.6.3)
    Installing execjs (2.0.1)
    Installing coffee-script (2.2.0)
    Installing thor (0.18.1)
    Using railties (4.1.0.beta) from source at .
    Installing coffee-rails (4.0.0)
    Installing dalli (2.6.4)
    Installing hike (1.2.3)
    Installing jquery-rails (2.2.2)
    Installing mustache (0.99.4)
    Installing mini_portile (0.5.1)
    Installing nokogiri (1.6.0)
    Installing kindlerb (0.1.1)
    Installing metaclass (0.0.1)
    Installing mocha (0.14.0)
    Installing multi_json (1.8.0)
    Installing mysql (2.9.1)
    Installing mysql2 (0.3.13)
    Installing pg (0.17.0)
    Installing racc (1.4.9)
    Installing rack-cache (1.2)
    Installing tilt (1.4.1)
    Installing sprockets (2.10.0)
    Installing sprockets-rails (2.0.0)
    Using rails (4.1.0.beta) from source at .
    Installing rdoc (3.12.2)
    Installing redcarpet (2.2.2)
    Installing sdoc (0.3.20)
    Installing sqlite3 (1.3.8)
    Installing turbolinks (1.3.0)
    Installing uglifier (2.2.1)
    Installing w3c_validators (1.2)
    Installing yajl-ruby (1.1.0)
    Your bundle is complete!
    Use `bundle show [gemname]` to see where a bundled gem is installed.
    Post-install message from rdoc:
    Depending on your version of ruby, you may need to install ruby rdoc/ri data:
    
    <= 1.8.6 : unsupported
     = 1.8.7 : gem install rdoc-data; rdoc-data --install
     = 1.9.1 : gem install rdoc-data; rdoc-data --install
    >= 1.9.2 : nothing to do! Yay!
    bundle install -j1  76.48s user 22.49s system 34% cpu 4:49.31 total

结果(-j8):

    ➜  ~    rvm gemset use bundler-j8 --create && gem install bundler --pre && cd /tmp/ && git clone https://github.com/rails/rails.git rails-j8 && cd rails-j8 && time bundle install -j8
    Using ruby-head with gemset bundler-j8
    Fetching: bundler-1.4.0.pre.2.gem (100%)
    Successfully installed bundler-1.4.0.pre.2
    Parsing documentation for bundler-1.4.0.pre.2
    Installing ri documentation for bundler-1.4.0.pre.2
    1 gem installed
    Cloning into 'rails-j8'...
    remote: Finding bitmap roots...
    remote: Counting objects: 383508, done.
    remote: Compressing objects: 100% (103485/103485), done.
    remote: Total 383508 (delta 277740), reused 382388 (delta 276656)
    Receiving objects: 100% (383508/383508), 94.56 MiB | 489 KiB/s, done.
    Resolving deltas: 100% (277740/277740), done.
    Fetching gem metadata from https://rubygems.org/.........
    Fetching gem metadata from https://rubygems.org/..
    Resolving dependencies...
    Installing builder (3.1.4)
    Installing atomic (1.1.14)
    Using rake (10.1.0)
    Installing minitest (5.0.8)
    Installing i18n (0.6.5)
    Installing rack (1.5.2)
    Installing polyglot (0.3.3)
    Using bundler (1.4.0.pre.2)
    Installing erubis (2.7.0)
    Installing json (1.8.0)
    Installing coffee-script-source (1.6.3)
    Installing mime-types (1.25)
    Installing benchmark-ips (1.2.0)
    Installing arel (4.0.0)
    Installing tzinfo (0.3.37)
    Installing mustache (0.99.4)
    Installing mini_portile (0.5.1)
    Installing hike (1.2.3)
    Installing execjs (2.0.1)
    Installing multi_json (1.8.0)
    Installing dalli (2.6.4)
    Installing metaclass (0.0.1)
    Installing bcrypt-ruby (3.1.2)
    Installing thor (0.18.1)
    Installing racc (1.4.9)
    Installing tilt (1.4.1)
    Installing mysql2 (0.3.13)
    Installing mysql (2.9.1)
    Installing thread_safe (0.1.3)
    Installing redcarpet (2.2.2)
    Installing rack-test (0.6.2)
    Installing coffee-script (2.2.0)
    Installing sqlite3 (1.3.8)
    Installing rdoc (3.12.2)
    Installing uglifier (2.2.1)
    Installing treetop (1.4.15)
    Installing rack-cache (1.2)
    Using activesupport (4.1.0.beta) from source at .
    Using actionpack (4.1.0.beta) from source at .
    Using activemodel (4.1.0.beta) from source at .
    Using railties (4.1.0.beta) from source at .
    Using actionview (4.1.0.beta) from source at .
    Using activerecord (4.1.0.beta) from source at .
    Installing sprockets (2.10.0)
    Installing yajl-ruby (1.1.0)
    Installing mocha (0.14.0)
    Installing pg (0.17.0)
    Installing coffee-rails (4.0.0)
    Installing jquery-rails (2.2.2)
    Installing sprockets-rails (2.0.0)
    Installing sdoc (0.3.20)
    Installing turbolinks (1.3.0)
    Installing mail (2.5.4)
    Using actionmailer (4.1.0.beta) from source at .
    Using rails (4.1.0.beta) from source at .
    Installing nokogiri (1.6.0)
    Installing kindlerb (0.1.1)
    Installing w3c_validators (1.2)
    Your bundle is complete!
    Use `bundle show [gemname]` to see where a bundled gem is installed.
    Post-install message from rdoc:
    Depending on your version of ruby, you may need to install ruby rdoc/ri data:
    
    <= 1.8.6 : unsupported
    = 1.8.7 : gem install rdoc-data; rdoc-data --install
    = 1.9.1 : gem install rdoc-data; rdoc-data --install
    >= 1.9.2 : nothing to do! Yay!
    bundle install -j8  77.96s user 22.92s system 59% cpu 2:49.20 total

从结果来看，CPU(user/system)时间变化不大，但是cpu利用率有明显提升，总消耗时间减少近一半。并发加快了总体安装的进程。

http://robots.thoughtbot.com/post/59584648154/parallel-gem-installing-using-bundler?utm_source=rubyweekly&utm_medium=email
