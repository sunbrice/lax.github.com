---
date: '2011-06-21 12:39:07'
layout: post
slug: rvm-ruby-version-manager
status: publish
title: 'RVM: Ruby Version Manager'
wordpress_id: '69'
categories: [Technology, Ruby]
tags:
- ruby
- rvm
---

[RVM: Ruby Version Manager - RVM Ruby Version Manager - Documentation](https://rvm.beginrescueend.com/).

管理Ruby版本的工具，可以帮助开发者在各个版本的Ruby之间进行快速切换，便于进行程序测试工作，也便于在生产环境中实现多版本程序共存。

你一定受够了服务器上默认版本与本地开发环境版本不一致的痛苦，试一下吧。

Setup steps:

    $ echo insecure >> .curlrc
    $ curl -L https://get.rvm.io | bash -s stable --ruby

Usage:

    $ rvm list
    $ rvm use system
    $ rvm use 1.9.2

另外，python也有类似工具(virtualenv)。
