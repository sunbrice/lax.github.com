---
layout: post
title: "使用Jekyll - Data Files简介"
date: 2014-06-29 16:40:13 +0800
comments: true
categories: 
tags: [jekyll]
---

使用 `Jekyll` 时，经常需要保存一些常用的数据，以便在模板中的随时调用。传统方式将数据写入 _config.yml 作为 `Site Variables` ，会造成 _config.yml 过大，而且无法将数据与配置分离，规模增长时带来管理方面的困扰。

Jekyll 最近引入 [Data Files][] 功能，用来满足保存数据的需求。这一功能使得可以避免在模板中重复书写数据，也能避免频繁修改全局设置。插件和页面风格中也可以利用 Data Files 来保存配置。 Jekyll 支持 `YAML` 和 `JSON` 格式的文件，这些文件需要保存在 `_data` 目录下（补充： `jekyll-2.1.0` 开始， Data Files 支持在 `_data` 目录下使用子目录来保存。）。

因为 Data Files 采用 `YAML` 和 `JSON` 格式，所以简单易上手。

让我们来一个网页列表的例子开始认识 Data Files 的神奇之处吧。

这个网页列表中保存了一些网页的名称，地址，以及可选的备注与描述；不同类型的网页保存在各个类型的子分类节点中；分类系统支持多级结构，使用 `meta: false` 表示该节点不包含网址元数据，而是用于包含其它节点的分类容器。


首先初始化模板文件 [links.html][] （只展示代码部分）。

{% highlight ruby %}
{% raw %}
{% include data_links.html nodes=site.data.nerd_urls %}
{% endraw %}
{% endhighlight %}

注意这段代码里的两个关键点：

1. 使用 `site.data.nerd_urls` 读取 `_data/nerd_urls.yml` 文件中报错的 `yaml` 数据。其中 `site.data.` 前缀用于读取 `Data File` ，之后的 `nerd_urls` 为不含扩展名的文件名。
2. `include` 另一个模板文件，并将数据使用 `nodes` 变量传值到模板文件中。

再来看一下 [data_links.html][] 这个文件。

{% highlight ruby %}
{% raw %}
<ul>{% for node in include.nodes %}
	<li>{% if node.meta == false and node.data %}
		{{ node.name }}
		{% include data_links.html nodes=node.data %}{% else %}
		<a href="{{ node.link }}">{{ node.name }}</a>{% if node.desc %}
		{{ node.desc }}{% endif %}
	{% endif %}</li>{% endfor %}
</ul>
{% endraw %}
{% endhighlight %}

这里使用传入的 `nodes` 变量（通过 `include.nodes` ），递归构建一个 `ul` 列表。

至此，这个页面的模板部分已经完成。

现在来补充数据文件 [nerd_urls.yml][] 。这是一个标准的 `YAML` 文件。

{% highlight ruby %}
- name: News & Information*
  meta: false
  data:
    - name: WebSites
      meta: false
      data:
        - name: LWN.net
          link: http://lwn.net
        - name: Lifehacker
          link: http://lifehacker.com
        - name: Hacker News
          link: http://news.ycombinator.com
    - name: Blogs (Person)
      meta: false
      data:
        - name: Steve Souders
          link: http://stevesouders.com
        - name: Pete Keen
          link: https://www.petekeen.net
        - name: Joe Armstrong
          link: http://joearms.github.io
{% endhighlight %}

最终生成的页面可以在 [http://blog.liulantao.com/links/](http://blog.liulantao.com/links/) 看到。

总结：本文通过简单的例子展示了 `Data Files` 的使用方法。
如果你有更好的想法欢迎留言交流。


参考文档：

*    [jekyll文档中关于Data Files的说明](http://jekyllrb.com/docs/datafiles/)
*    [文章相关源代码](https://github.com/Lax/lax.github.com/commit/3caa16daa0c258a50fcdf56c6018dfeecfa0950c)

[Data Files]: <http://jekyllrb.com/docs/datafiles/> "Data Files"
[links.html]: <https://github.com/Lax/lax.github.com/blob/lax.github.com-jekyllrb/_pages/links.html> "links.html"
[data_links.html]: <https://github.com/Lax/lax.github.com/blob/lax.github.com-jekyllrb/_includes/data_links.html> "_includes/data_links.html"
[nerd_urls.yml]: <https://github.com/Lax/lax.github.com/blob/lax.github.com-jekyllrb/_data/nerd_urls.yml> "_data/nerd_urls.yml"
