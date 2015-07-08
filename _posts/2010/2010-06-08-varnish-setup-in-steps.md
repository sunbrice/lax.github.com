---
layout: post
name: varnish-setup-in-steps
title: Varnish Setup in Steps
time: 2010-06-08 14:44:00.000000000 +08:00
categories: [Technology]
tags: [Varnish]
---
配置与编译

    ./autogen.sh
    ./configure
    make

测试

    cd bin/varnishtest && ./varnishtest tests/*.vtc


安装

    make install

启动

    /data/varnish/sbin/varnishd -a 0.0.0.0:80 -s file,/data/vcache/varnish_cache.data,20g -f /data/varnish/etc/vcl.conf -p thread_pool_max=1500 -p thread_pools=8 -p listen_depth=512 -P /data/varnish/sbin/varnish.pid

重新载入

    kill -HUP `cat /data/varnish/sbin/varnish.pid`

关闭

    killall varnishd

-- <br>Liu Lantao<br>EMAIL: liulantao ( at ) gmail ( dot ) com ; <br>WEBSITE: <a href="http://www.liulantao.com/">http://www.liulantao.com/</a> .<br>  <div class="blogger-post-footer"><img width='1' height='1' src='https://blogger.googleusercontent.com/tracker/31667564-2499494717203567834?l=sandshoots.blogspot.com' alt='' /></div>
