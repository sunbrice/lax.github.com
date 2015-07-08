---
layout: post
title: "UEK R3升级手记"
description: ""
categories: [Technology]
tags: [Linux]
---

UEK是OracleLinux发布的内核版本，对内核增加了更多优化。最近发布了UEK R3。如果需要部署3.x系列内核，可以基于此版本进行测试升级了。

主要的更新内容包括：

*   基于Linux主线版本3.8.13
*   虚拟化相关支持升级
*   btrfs升级（UEK R2中基于kernel 3.0，UEK R3基于kernel 3.8）
*   DTrace 0.4
*   cgroups和LXC更新。lxc-0.9.0-2.0.4同时发布
*   XEN改进
*   大量的驱动升级，涵盖存储设备、网络设备等

重要改进：

*   ext4支持inode内存储小文件（通过-O inline_data）选项
        mkfs.ext4 -O inline_data device
    针对已有的ext4文件系统
        tune2fs -O inline_data device
*   XFS日志checksum
*   zero huge page以改善现有zero 4-KB page的性能
*   NUMA节点下内存分配的自动平衡
*   memory control group支持额外的参数
    *   memory.kmem.failcnt
    *   memory.kmem.limit_in_bytes
    *   memory.kmem.max_usage_in_bytes
    *   memory.kmem.usage_in_bytes
*   SCSI error-handling超时值可以通过/sys/class/scsi_device/*/device/eh_timeout设置
*   通过mmap()的flags或shmget()的shmflg参数可以使用variable-sized huge page
*   TCP fast open (TFO)
*   watchdog定时器设备/dev/watchdog

注意事项

*   I/O scheduler默认为deadline。RHEL中默认为cfq


安装UEK R3。

首先需要安装Oracle仓库。安装oracle仓库的方法参考[http://public-yum.oracle.com](http://public-yum.oracle.com)。或者以root身份执行以下命令

    #CentOS 5
    cd /etc/yum.repos.d
    wget https://public-yum.oracle.com/public-yum-el5.repo
    
    #CentOS 6
    cd /etc/yum.repos.d
    wget https://public-yum.oracle.com/public-yum-ol6.repo

由于UEK R3还没有加入到正式版本中，所有需要手工设置使用oracle的beta仓库（目前属于测试阶段，以后可能会更改）

    cd /etc/yum.repos.d
    wget http://public-yum.oracle.com/beta/public-yum-ol6-beta.repo
    sed -i s/enabled=0/enabled=1/g public-yum-ol6-beta.repo
或
    cat >/etc/yum.repos.d/public-yum-ol6-beta.repo<<EOF
    [ol6_beta]
    name=Oracle Linux \$releasever Beta (\$basearch)
    baseurl=http://public-yum.oracle.com/beta/repo/OracleLinux/OL6/uek3/\$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=0
    enabled=1
    EOF

现在可以通过yum来安装uek3：
    yum install -y kernel-uek-3.8.13-13.el6uek
    
    [root@localhost yum.repos.d]# yum install kernel-uek-3.8.13-13.el6uek -y
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: mirrors.btte.net
     * epel: mirrors.hust.edu.cn
     * extras: mirrors.btte.net
     * updates: mirrors.hust.edu.cn
    Setting up Install Process
    Resolving Dependencies
    --> Running transaction check
    ---> Package kernel-uek.x86_64 0:3.8.13-13.el6uek will be installed
    --> Processing Dependency: kernel-firmware = 3.8.13-13.el6uek for package: kernel-uek-3.8.13-13.el6uek.x86_64
    --> Running transaction check
    ---> Package kernel-uek-firmware.noarch 0:3.8.13-13.el6uek will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    ========================================================================================================================
     Package                            Arch                  Version                         Repository               Size
    ========================================================================================================================
    Installing:
     kernel-uek                         x86_64                3.8.13-13.el6uek                ol6_beta                 36 M
    Installing for dependencies:
     kernel-uek-firmware                noarch                3.8.13-13.el6uek                ol6_beta                1.6 M
    
    Transaction Summary
    ========================================================================================================================
    Install       2 Package(s)
    
    Total download size: 37 M
    Installed size: 151 M
    Downloading Packages:
    (1/2): kernel-uek-3.8.13-13.el6uek.x86_64.rpm                                                    |  36 MB     02:22     
    (2/2): kernel-uek-firmware-3.8.13-13.el6uek.noarch.rpm                                           | 1.6 MB     00:05     
    ------------------------------------------------------------------------------------------------------------------------
    Total                                                                                   256 kB/s |  37 MB     02:29     
    Running rpm_check_debug
    Running Transaction Test
    Transaction Test Succeeded
    Running Transaction
      Installing : kernel-uek-firmware-3.8.13-13.el6uek.noarch                                                          1/2 
      Installing : kernel-uek-3.8.13-13.el6uek.x86_64                                                                   2/2 
      Verifying  : kernel-uek-firmware-3.8.13-13.el6uek.noarch                                                          1/2 
      Verifying  : kernel-uek-3.8.13-13.el6uek.x86_64                                                                   2/2 
    
    Installed:
      kernel-uek.x86_64 0:3.8.13-13.el6uek                                                                                  
    
    Dependency Installed:
      kernel-uek-firmware.noarch 0:3.8.13-13.el6uek                                                                         
    
    Complete!

现在可以在/boot目录下和/etc/grub/menu.lst里看到3.8.13版本的内核项。重启试试。

    uname -a
    Linux localhost.localdomain 3.8.13-13.el6uek.x86_64 #1 SMP Wed Aug 21 14:28:36 PDT 2013 x86_64 x86_64 x86_64 GNU/Linux

还可以安装其它工具

    # yum install dtrace-utils
    # yum install lxc


[Release Notes](http://linux.oracle.com/RELEASE-NOTES-UEK3-BETA-en.html)

