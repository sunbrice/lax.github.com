---
layout: post
title: "Nginx中配置Access Control"
date: 2014-06-05 10:58:43 +0800
comments: true
categories: Technology

---

最近处理了一个```Nginx```的```ACL```问题，记录一下处理过程。

公司线上服务使用```Nginx```做前端的负载分发。对于安全原因屏蔽客户端IP的需求，在这一个层次操作，使用```ngx_http_access_module```提供的```allow```/```deny```语法进行配置。

	# nginx.conf
	events {
		use epoll;
		worker_connections  65535;
	}
	
	http {
		# ACL
		include acl.conf;

		# Vhost
		include vhosts/*.conf;
	}

全局```ACL```配置在单独的文件```acl.conf```中。

	# acl.conf
	deny 123.45.67.89;

```vhost```配置文件中没有```ACL```配置。

	# vhosts/www.example.com.conf
	server {
		listen	80 default;
		server_name	www.example.com;
	
		location / {
			root html/;
		}
	}
	
	# vhosts/api.example.com.conf
	server {
		listen	80;
		server_name	api.example.com;
	
		location / {
			proxy_pass http://backend_api;
		}
	}

基于这种配置模式，每当有```ACL```需求时，只要更新```acl.conf```的ip列表即可。

### 问题

周一上班时，前一天值班的同事提到值班时遇到一个问题，使用```deny```失效了。

查看了值班的邮件记录及操作记录，是这样的一些情况：

1. 安全组提出封禁IP需求。值班同事将涉事IP段（ip1）加入```acl.conf```
2. API组提出封禁IP需求，且注明只针对api域名封禁。值班同时将设施IP端（ip2）加入```vhost```配置文件，如下

第2步操作如下

	vhosts/api.example.com.conf
	server {
		listen	80;
		server_name	api.example.com;
	+	deny ip2;
		
		location / {
			proxy_pass http://backend_api;
		}
	}

在第二步操作之后，又收到安全组提供的IP段（ip3），加入```acl.conf```后，仍然有来自ip3的请求能获得```200```返回。

现在的配置情况简化表示为

	http {
		deny ip1;
		server {
			server_name api.example.com;
			deny ip2;
		}
		deny ip3;
	}

经过测试确认，上述配置的最终现象为server ```api.example.com```中只有```deny ip2```生效，```deny ip3```没有生效。

### 追因

查看```Nginx```源码```src/http/modules/ngx_http_access_module.c```

在配置文件处理阶段有两部分需要关注。

#### 第一个关注点 ```ngx_http_access_rule```函数

在出现```allow```/```deny```语法时执行。

它的作用是维护每个作用域范围内的```alcf->rules```，这是一个ACL列表。

	static char *
	ngx_http_access_rule(ngx_conf_t *cf, ngx_command_t *cmd, void *conf)

		...

	    default: /* AF_INET */

	        if (alcf->rules == NULL) {
	            alcf->rules = ngx_array_create(cf->pool, 4, sizeof(ngx_http_access_rule_t));
	            if (alcf->rules == NULL) {
	                return NGX_CONF_ERROR;
	            }
	        }
	
	        rule = ngx_array_push(alcf->rules);
	        if (rule == NULL) {
	            return NGX_CONF_ERROR;
	        }
	
	        rule->mask = cidr.u.in.mask;
	        rule->addr = cidr.u.in.addr;
	        rule->deny = (value[0].data[0] == 'd') ? 1 : 0;


#### 第二个关注点 ```ngx_http_access_merge_loc_conf```函数

在解决嵌套定义时执行。```parent```代表上一级配置，```child```代表下一级配置。

上一级与下一级是一个相对概念，```http```相对```server```为上一级，```server```为```http```下一级；```server```相对```location```为上一级，```location```为```server```下一级。

从下面代码可以看出，如果当前ACL(```child->rules```)为空，则继承上一级的ACL(```parent->rules```)。这解释了当```http```中定义```deny```而```server```中不定义时，```http```中的```deny```生效。

另外也证实了一个事实，即当前级别中定义过ACL之后，不会与上一级的ACL进行列表合并，只有当前列表生效。所以会出现前文提到的现象，```server```中定义```deny```后，```http```中的```deny```规则失效了。

	static char *
	ngx_http_access_merge_loc_conf(ngx_conf_t *cf, void *parent, void *child)
	{
	    ngx_http_access_loc_conf_t  *prev = parent;
	    ngx_http_access_loc_conf_t  *conf = child;

		...

	    if (conf->rules == NULL) {
	        conf->rules = prev->rules;
	    }

		...

	    return NGX_CONF_OK;
	}

#### 探讨

上面ACL中，我们按先验经验想当然认为```allow```/```deny```会如其它```nginx```语法一样，在不同级别之间有继承关系，而事实证明这种想法是错误的。

由于不同层级之间的ACL列表独立维护，而```Nginx```在进行处理是只针对当前的rules遍历，一个不太严谨但是有助于理解的看法是可以认为在当前配置中增加了一个默认```allow all```。

在```Apache```中也有ACL相关配置，由于配置语法格式比较清晰，一般在出现嵌套时不会出现误解。
