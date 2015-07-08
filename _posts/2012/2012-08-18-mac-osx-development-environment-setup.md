---
layout: post
title: "Mac OSX development environment setup"
description: ""
categories: [Technology]
tags: [xcode, git, homebrew, rvm, textmate, macruby]
---

What to setup?
==============

*  xcode
*  git
*  Homebrew
*  RVM: Ruby Version Manager
*  textmate: popular code editor
*  macruby

PROGRESSING
===========

xcode
-----
Install from App Store. This very difficulty is that it takes too long to download, especially in China.

I swithed my local DNS to a Taiwan host (168.95.1.1), so it finished in serveral hours.

git
---
Download recent stable release of git for mac from [git-scm.com](http://git-scm.com/downloads)

Install other tools related to git: 
*  [GitHub for Mac](http://mac.github.com) 
*  [GitStats](https://github.com/trybeee/GitStats): generate stats web pages for your project  


Homebrew
--------
Simple installation with one-line-command: 

    ruby <(curl -fsSkL raw.github.com/mxcl/homebrew/go)

Usage example: 

    brew search textmate
    brew install ninja  # need when building textmate
    brew install wget


RVM
---
[RVM](https://rvm.io) is short for Ruby Version Manager, not Ruby Virtual Machine. 

Install stable version with default ruby[-1.9.3] 

    curl -kL https://get.rvm.io | bash -s stable --ruby

When every thing reports ok, run this command to see installed versions of ruby in a new term: 

    rvm list

Switch to system version 

    rvm use system
    rvm use system --default


textmate
--------
I choose build textmate from source. follow [this document](https://github.com/textmate/textmate/#building) 

First, install dependencies 

    brew install ragel boost multimarkdown hg ninja

Get and prepare source code 

    git clone git://github.com/textmate/textmate.git
    cd textmate
    git submodule update --init

Configure and build 

    ./configure && ninja


macruby
-------
[MacRuby](http://macruby.org) is an implementation of Ruby 1.9 directly on top of Mac OS X core technologies. 

Install with rvm 

    rvm install macruby-head

[RubyMotion](http://www.rubymotion.com) for iOS development. 
