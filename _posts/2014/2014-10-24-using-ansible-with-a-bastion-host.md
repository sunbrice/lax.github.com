---
title: "通过堡垒机代理SSH运行Ansible（译）"
layout: post
date: "2014-10-24 10:30:00"
comments: true
categories: Technology
tags: [Linux, Ansible]
---

有一种常见的网络安全模式是阻止私有网络外部对应用服务器的所有连接（指除了业务数据外其它的连接，如后台管理系统和内部业务系统。译者注），然后使用 [DMZ](http://en.wikipedia.org/wiki/DMZ_%28computing%29) 区域中的 [堡垒机](http://en.wikipedia.org/wiki/Bastion_host) 来选择性的将到服务器的流量加入白名单。

<!--excerpt-->

我们有这样的一个服务器池，只允许来自特定 IP 地址的 SSH 流量。这些服务器还由 [Ansible](http://www.ansible.com/) 通过 SSH 方式程序化的来管理。

堡垒机方式导致 Ansible 不能直接与应用服务器通讯，因此需要找到通过堡垒机代理 SSH 连接的方法。

我喜欢用 Ansible 创建简单任务来运行，比如清空 memcache 以消除缓存。运行这个例子，以下是 Ansible 结构：


    devops/
        ansible/
            roles/
                memcache/
                    tasks/
                        main.yml
                        restart.yml
            tasks/
                restart-memcache.yml
            vars/
                production-memcache.yml
        bin/
            restart-production-memcache.sh
        hosts.ini
        ssh.config
        ansible.cfg


脚本 `tasks/restart-production-memcache.sh` 如下：


    #!/bin/sh
    ssh-add ${DEPLOY_KEYS_DIR}/memcache-servers.pem
    
    ansible-playbook -i ansible/hosts.ini -u ansible ansible/tasks/restart-memcache.yml -v


从 `devops` 根目录执行，首先将服务器的 SSH key 添加到 SSH 客户端代理，然后执行 `restart-memcache.yml` playbook ，这包含了 memcache 角色的 `restart.yml` playbook （以及执行其它任务）。

`ssh.config` 文件中有以下 SSH 配置：


    Host bastion
        User                   ec2-user
        HostName               ###.###.###.###
        ProxyCommand           none
        IdentityFile           /path/to/ssh/key.pem
        BatchMode              yes
        PasswordAuthentication no
    
    Host *
        ServerAliveInterval    60
        TCPKeepAlive           yes
        ProxyCommand           ssh -q -A ec2-user@###.###.###.### nc %h %p
        ControlMaster          auto
        ControlPath            ~/.ssh/mux-%r@%h:%p
        ControlPersist         8h
        User                   ansible
        IdentityFile           /path/to/ssh/key.pem


首先声明用于连接到堡垒机的配置。紧接着是一个包含所有主机的总配置，在 `ProxyCommand` 中指明首先连接到堡垒机，然后使用 netcat (`nc) 来传递 Ansible 命令到应用服务器。

在 `devops` 文件夹中运行 `ssh bastion -F ssh.config` 即可连接到堡垒机。

接下来 Ansible 在连接应用服务器时，需要被告知使用这个自定义的 SSH 配置。

`ansible.cfg` 文件中有如下配置：


    [ssh_connection]
    ssh_args = -o ControlPersist=15m -F ssh.config -q
    scp_if_ssh = True
    control_path = ~/.ssh/mux-%%r@%%h:%%p


当 Ansible 在 `devops` 中执行时，能自动选择 `ansible.cfg` 并在运行 playbooks 时使用定义的配置项。

这种设置方法的一个问题是它运行时的 Ansible 的输出非常冗长，因为通过堡垒机连接到应用服务器时，包含了 SSH 调试连接信息；暂未找到好的办法来跳过这些信息。

--------

译者：[Liu Lantao](https://github.com/Lax) : [http://blog.liulantao.com](http://blog.liulantao.com/)

来源：
[Using Ansible with a bastion SSH host](http://alexbilbie.com/2014/07/using-ansible-with-a-bastion-host/) by [Alex Bilbie](https://github.com/alexbilbie)
