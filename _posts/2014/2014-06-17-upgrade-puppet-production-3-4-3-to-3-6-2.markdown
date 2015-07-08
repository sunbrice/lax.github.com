---
layout: post
title: "生产环境Puppet升级笔记（3.4.3→3.6.2）"
date: 2014-06-17 14:28:10 +0800
comments: true
categories: Techonology
tags: Puppet

---

线上Puppet部署时采用的是版本3.4.3，最近发现客户端经常有一些warning提示，从提示信息看出涉及到跨版本的功能变化，因此对版本进行了一个有计划的升级。

puppetlabs的最新版本为3.6.2。开始升级之前参考了官方的Release Notes([3.5][],[3.6][])和其他人的文档([PUPPET 3.6.1 UPDATES][])。

首先将yum仓库同步到yum.puppetlabs.com的最新状态，方便后续使用。

升级分为master和agent两部分，分别进行调整。

## master调整

master有两个明显的变化

1.     `environmentpath`支持
2.     Package模块中引入的`allow_virtual`


### environmentpath调整

关于`environmentpath`，配置文件改动比较小，只需修改`puppet.conf`中的`[master]`部分

    +    environmentpath = $confdir/environments
    -    modulepath = $confdir/modules:/usr/share/puppet/modules
    -    manifestdir = $confdir/manifests

早期版本的`envorinment`支持通过在puppet.conf中额外的`[production]`和`[testing]`部分实现。需要删除`puppet.conf`中自定义的environment部分。

同时需要对目录进行调整，这部分工作量稍大，也在可接受的范围内。以下示例中星号(*)标记的目录对应调整即可。

调整之前目录结构为

    .
    ├── auth.conf
    ├── autosign.conf
    ├── puppet.conf
    ├── manifests
    │   ├── production   *
    │   └── testing    *
    ├── modules
    │   ├── production   *
    │   └── testing    *
    ├── reports
    ├── ssl
    ├── var
    └── yaml


调整之后目录结构为

    .
    ├── auth.conf
    ├── autosign.conf
    ├── puppet.conf
    ├── environments
    │   ├── production
    │   │   ├── manifests *
    │   │   └── modules   *
    │   └── testing
    │       ├── manifests *
    │       └── modules   *
    ├── reports
    ├── ssl
    ├── var
    └── yaml

### allow_virtual调整

这是一个新增的特性，相关背景可参考[PDP-897][]。由于默认值变化，如果不配置agent会收到一个warning信息。

修改`environments/production/manifests/site.pp`和`environments/testing/manifests/site.pp`

    +    Package {
    +      allow_virtual => true,
    +    }


### 软件包升级

通过gem升级安装`puppet-3.6.2`

    gem install puppet --version 3.6.2

安装后重新启动puppet master即可。


## agent调整


master端设置的`allow_virtual`是新增的一个特性，如果agent不支持，会报错，将agent的puppet版本并重启进程即可。

    Error: Failed to apply catalog: Invalid parameter allow_virtual


理论上直接升级客户端puppet到3.6.2版本即可完成。不过在升级后发现使用`puppet agent --genconfig`生成的配置文件仍然会有warning。

    Warning: Setting manifest is deprecated in puppet.conf. See http://links.puppetlabs.com/env-settings-deprecations
       (at /usr/lib/ruby/site_ruby/1.8/puppet/settings.rb:1067:in `each')
    Warning: Setting modulepath is deprecated in puppet.conf. See http://links.puppetlabs.com/env-settings-deprecations
       (at /usr/lib/ruby/site_ruby/1.8/puppet/settings.rb:1067:in `each')
    Warning: Setting templatedir is deprecated. See http://links.puppetlabs.com/env-settings-deprecations
       (at /usr/lib/ruby/site_ruby/1.8/puppet/settings.rb:1071:in `each')
    Warning: Setting manifestdir is deprecated. See http://links.puppetlabs.com/env-settings-deprecations
       (at /usr/lib/ruby/site_ruby/1.8/puppet/settings.rb:1071:in `each')


直接根据提示取消指定的配置项即可消除警告。这里使用一个脚本进行修改。

    #!/bin/bash
    
    for s in manifest modulepath templatedir manifestdir
    do
    	grep "^    $s = " /etc/puppet/puppet.conf
    	sed -i -e "/^    $s = /s/^/#/" /etc/puppet/puppet.conf
    done

使用调整后的配置文件启动`puppet agent -t`，可以看到成功运行并且不再有warning。


[3.5]: <http://docs.puppetlabs.com/puppet/3.5/reference/release_notes.html>  "Puppet 3.5 Release Notes"
[3.6]: <http://docs.puppetlabs.com/puppet/3.6/reference/release_notes.html>  "Puppet 3.6 Release Notes"
[PUPPET 3.6.1 UPDATES]: <http://rnelson0.com/2014/06/05/puppet-3-6-1-updates/>  "PUPPET 3.6.1 UPDATES"
[PDP-897]: <https://tickets.puppetlabs.com/browse/PUP-897>
