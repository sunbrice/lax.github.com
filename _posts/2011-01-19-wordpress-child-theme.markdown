---
date: '2011-01-19 12:55:26'
layout: post
slug: wordpress-child-theme
status: publish
title: wordpress child-theme
wordpress_id: '37'
categories:
- Technology
tags:
- child-theme
- theme
- wordpress
---

wordpress内置的theme引擎，很适合以已有的风格为基础，修改某一部分的风格文件——这种修改对于个人blog是很常见的行为。直接修改theme文件，与使用child-theme的最大区别，在于当官方升级theme包时，个人所作修改是否会被覆盖/丢弃。

themes/twentyten-child $ cat style.css

/*
* Theme Name:     Twenty Ten Child
* Theme URI:      http: //www.liulantao.com/portal/
* Description:    Child theme for the Twenty Ten theme
* Author:         Liu Lantao
* Author URI:     http: //www.liulantao.com/portal/
* Template:       twentyten
* Version:        0.1.0
* */

@import url("../twentyten/style.css");

#site-title a {
color: #000099;
}



http://codex.wordpress.org/Child_Themes
