---
layout: post
title: "测试驱动开发 Ruby 命令行工具实战"
description: ""
categories: [Technology]
tags: [Ruby, Tools, TravisCI, RubyGems]
---

**TL;DR;**
`TDD` (测试驱动开发)是敏捷开发中的一项核心实践和技术，也是一种设计方法论。
`TDD` 的原理是在开发功能代码之前，先编写单元测试用例代码，测试代码确定需要编写什么产品代码。

`to_yaml` 是一款命令行工具，*将 `JSON` 输入转为 `YAML` 文本输出*。

本文介绍了 `to_yaml` 的开发过程中如何采用 `TDD` 方法开发功能，以及用到的免费服务 `GitHub` / `TravisCI` / `RubyGems`。

### 背景

先从最近使用的 `ElasticSearch` 说起。
作为通用的日志收集、分析与展示的工具集，`ELK` 工具栈已经相当普及。
其中在管理 `ElasticSearch` 集群时，大部分时间都要使用 `HTTP` 接口跟 `JSON` 格式数据打交道。

ES 输出 `JSON` 数据内容比较多，即使使用 `?pretty` 参数，仍然难看清数据的层次关系。
`?pretty` 的一个副作用是输出内容过长，浪费了大量的屏幕纵向空间。

使用 `YAML` 格式能够在很大程度上缓解空间的问题。
基于这个想法，做了一个简单的工具出来，发布在了 [RubyGems.org](https://rubygems.org)。

### 初步想法

在实际使用 `JSON` 时，希望的是能直接将接口输出内容直接转换为 `YAML` 格式。
如这样的形式：

    $ curl -s -XGET http://localhost:9200/_mappings | to_yaml

另外，要把这个工具作为`软件包`发布出来，方便别的地方使用。
`ruby` 程序的第一选择即是 `gem`。

使用 `gem` 发布还有一个好处：可以利用 `ruby` 的开发工具，如 `bundle`、`rspec` 等，来发布质量可控的软件。

### 开始动手

#### 创建 `gem` 框架目录

现阶段最简单的工具是 `bundle gem`。

{% highlight bash %}
$ bundle gem to_yaml
Creating gem 'to_yaml'...
Code of conduct enabled in config
MIT License enabled in config
      create  to_yaml/Gemfile
      create  to_yaml/.gitignore
      create  to_yaml/lib/to_yaml.rb
      create  to_yaml/lib/to_yaml/version.rb
      create  to_yaml/to_yaml.gemspec
      create  to_yaml/Rakefile
      create  to_yaml/README.md
      create  to_yaml/bin/console
      create  to_yaml/bin/setup
      create  to_yaml/CODE_OF_CONDUCT.md
      create  to_yaml/LICENSE.txt
      create  to_yaml/.travis.yml
      create  to_yaml/.rspec
      create  to_yaml/spec/spec_helper.rb
      create  to_yaml/spec/to_yaml_spec.rb
Initializing git repo in /private/tmp/to_yaml
{% endhighlight %}

更改 `to_yaml.gemspec` 中的基本信息，所有标记 `TODO` 的全酌情修改。

在 `github` 上创建一个代码库。然后
`git add . && git commit -am Init && git push`。

现在我们已经有了基本的框架。

#### **代码未动，测试先行**

查看 `Rakefile` 内容如下

{% highlight ruby %}
require "bundler/gem_tasks"
require "rspec/core/rake_task"

RSpec::Core::RakeTask.new(:spec)

task :default => :spec
{% endhighlight %}

命令行执行 `rake spec` 或 `rake`，可以执行默认的测试用例。

来让我们为 `to_yaml` 工具写一个最简单的用例。

打开 `spec/to_yaml_spec.rb` 文件，在 `describe ToYaml` 与对应 `end` 直接增加一个用例

{% highlight ruby %}
  it 'parse json with cli' do
    json_str = '{"a": [1, 2, 3], "b": {"c": "d"}}'
    yaml_str = '---\na:\n- 1\n- 2\n- 3\nb:\n  c: d\n'

    cli_output = run_cli('./exe/to_yaml', json_str)
    expect(cli_output).to eq(yaml_str)
  end
{% endhighlight %}

修改 `spec/spec_helper.rb`，加入 helper 方法

{% highlight ruby %}
require 'open3'

def run_cli(exe="./exe/to_yaml", input)
  cmd = %Q{echo \'#{input}\'| bundle exec #{exe}}
  output = Open3.popen3(cmd) { |stdin, stdout, stderr, wait_thr| stdout.read }
end
{% endhighlight %}

注意这里面，我们构造一个 `JSON` 字符串 `json_str`，和一个 `YAML` 字符串 `yaml_str`。
期望的行为是运行 `./exe/to_yaml` 命令处理 `json_str` 后，输出 `cli_output` 与 `yaml_str` 一致。

现在执行这个测试 `rake spec` 或 `rake`，毫无悬念失败了。

不过这是一个好的开头，我们有了明确的目标：让测试成功。

#### 创建命令行

在 `to_yaml.gemspec` 中，通过 `spec.executables` 指定了可执行文件的路径为 `./exe/` 目录。

{% highlight ruby %}
  spec.executables   = spec.files.grep(%r{^exe/}) { |f| File.basename(f) }
{% endhighlight %}

`to_yaml` 命令文件就放在这里。

现在创建 `exe/to_yaml` 文件，加入内容：

{% highlight ruby %}
#!/usr/bin/env ruby

require 'json'
require 'yaml'

puts JSON.load(STDIN).to_yaml
{% endhighlight %}

再简单不过的一个单行脚本。
按我们的设想，从 `STDIN` 读入内容，转化为 `YAML` 格式。
本着 `[YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)` 原则，没做任何额外的容错 - -!

现在执行测试

{% highlight bash %}
$ rake spec

ToYaml
  has a version number
  parse json with cli

Finished in 0.77046 seconds (files took 0.09961 seconds to load)
2 examples, 0 failures
{% endhighlight %}

太棒了，测试用例全部成功。

#### 测试，**AGAIN**

目前为止的测试还在靠手工驱动执行。
而在实践中，则需要**将持续构建整合进开发流程**，所有状态都**可以追溯**，并能**及时反馈构建结果**。

在 `gem` 目录下，我们可以找到一个 `.travis.yml` 文件。
[travis-ci](https://travis-ci.org) 是开源世界应用最普遍的持续集成工具。

使用 `travis-ci` 需要先做设置。
到 https://travis-ci.org，使用 `github` 帐号登录，并从 `github` 中添加我们的 `to_yaml` 项目。

设置完毕，下次我们 push 代码到 `github`，`travis-ci` 可以自动开始构建。
为了方便从 `GitHub` 页面看到项目 Build 状态，在 `README.md` 文件里增加项目的状态图标

{% highlight bash %}
[![Build Status](https://travis-ci.org/Lax/to_yaml.svg?branch=master)](https://travis-ci.org/Lax/to_yaml)
{% endhighlight %}

刚才已经在本地测试成功，现在将代码 commit 并 push 到 `github`。
从 `travis-ci` 页面，我们看到已经创建了第一次构建 [Build #1](https://travis-ci.org/Lax/to_yaml/builds/71275189)。
很幸运，这次构建到结果也成功了！

#### 发布

`rake release` 可以自动创建一个发布 tag，将代码 push 到 `github`。
并 build 出 to_yaml.gem，通过 `gem push` 发布到 rubygems.org。

### 使用

下次使用时，直接 `gem install to_yaml`，既可以在 `PATH` 中搜索到 `to_yaml` 执行程序。

测试一个公开的 `JSON` 服务：

{% highlight bash %}
$ curl -s http://jsonip.com
{"ip":"123.45.67.89","about":"/about","Pro!":"http://getjsonip.com"}

$ curl -s http://jsonip.com | to_yaml
---
ip: 123.45.67.89
about: /about
Pro!: http://getjsonip.com
{% endhighlight %}

很清爽，有木有！

### 总结展望

本文介绍的是一个极简工具的开发过程，希望你能感受到测试驱动开发所发挥的作用。

作者在实际项目中也尽可能实践 `TDD` 方法，发布的工具如 [aliyun.gem](https://github.com/Lax/aliyun) (一个阿里云API的客户端) 也采用了类似流程。
甚至[本博客](https://blog.liulantao.com)也引入了 travis-ci 做持续构建和测试，确保内容中引用的图片和链接无死链。

如果你对本文描述的内容感兴趣，欢迎发布评论来交流。
