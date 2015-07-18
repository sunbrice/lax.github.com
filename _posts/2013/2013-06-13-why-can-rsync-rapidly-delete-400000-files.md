---
layout: post
title: "为什么rsync能够快速删除400000文件？"
description: ""
categories: [Technology]
tags: [rsync, rm, DTrace, dtruss, SystemTap]
---


### 背景

Quora上一篇文章[★How can someone rapidly delete 400,000 files?](http://www.quora.com/File-Systems/How-can-someone-rapidly-delete-400-000-files)提到通过rsync能够快速删除大量文件，之后在《Linux技巧：一次删除一百万个文件的最快方法》这篇文章里做了一个详细的评测，对于rm/find/rsync等诸多方法的性能做了对比。

对于出现性能的差异，应该属于预料中的结果。
为了验证这个现象，我模拟了Quora原提问的要求，创建了40万个文件，分别用rm和rsync进行删除操作，对syscall做统计。
为了简化条件，这写文件全部是空文件（使用包含内容的文件对结果不会造成明显差异，有兴趣可以重试一下）。

统计syscall使用了dtruss工具，这是MacOSX上提供的syscall调试工具，基于DTrace。Linux上可以使用SystemTap来代替。


### 验证测试步骤

#### 第一步，创建测试文件

    mkdir tmp/; seq 1 400000 | xargs -I{} touch tmp/file_{}


创建的目录文件大小约为13M。这个大小指的是*目录文件*，不包含目录中文件，要注意。

    $ ls -dl tmp/
    drwxr-xr-x  56513 lax  wheel  13062562  6 13 16:18 tmp-test-rsync


#### 第二步，使用dtruss执行rm命令测试

    $sudo dtruss -c 'rm -rf tmp-test-rm/'

    CALL                                        COUNT
    __mac_syscall                                   1
    audit_session_self                              1
    bsdthread_register                              1
    exit                                            1
    fstatfs64                                       1
    getaudit_addr                                   1
    getegid                                         1
    rmdir                                           1
    shared_region_check_np                          1
    thread_selfid                                   1
    __sysctl                                        2
    close                                           2
    close_nocancel                                  2
    csops                                           2
    getpid                                          2
    ioctl                                           2
    issetugid                                       2
    open                                            2
    open_nocancel                                   2
    pread                                           2
    geteuid                                         3
    fchdir                                          4
    mprotect                                        8
    stat64                                         34
    mmap                                           93
    munmap                                        184
    madvise                                       205
    getdirentries64                              2659
    lstat64                                    311901
    unlink                                     400000


#### 第三步，使用dtruss执行rsync命令测试

    $sudo dtruss -c 'rsync -a --delete empty/ tmp/'

    CALL                                        COUNT
    __mac_syscall                                   1
    __pthread_canceled                              1
    audit_session_self                              1
    bsdthread_register                              1
    exit                                            1
    fcntl_nocancel                                  1
    fork                                            1
    fstatfs64                                       1
    getaudit_addr                                   1
    getegid                                         1
    getrlimit                                       1
    getuid                                          1
    ioctl                                           1
    lstat64                                         1
    shared_region_check_np                          1
    shm_open                                        1
    sigprocmask                                     1
    sigreturn                                       1
    thread_selfid                                   1
    umask                                           1
    __sysctl                                        2
    chdir                                           2
    csops                                           2
    getdirentries64                                 2
    getpid                                          2
    lseek                                           2
    pread                                           2
    socketpair                                      2
    fstat64                                         3
    munmap                                          3
    open_nocancel                                   3
    close                                           4
    close_nocancel                                  4
    geteuid                                         4
    issetugid                                       4
    open                                            4
    wait4                                           6
    read_nocancel                                   7
    write                                           7
    mprotect                                        8
    read                                           10
    sigaction                                      10
    fcntl                                          11
    mmap                                           13
    select                                         21
    stat64                                         35


### 现象分析

*   rm
    *   rm命令大量调用了lstat64和unlink，可以推测删除每个文件前都从文件系统中做过一次lstat操作。
    *   lstat64的次数低于文件总数，还有另外的原因，之后会在另一篇文章中说明。
    *   getdirentries64这个调用比较关键。
    *   过程：正式删除工作的第一阶段，需要通过getdirentries64调用，分批读取目录（每次大约为4K），在内存中建立rm的文件列表；第二阶段，lstat64确定所有文件的状态；第三阶段，通过unlink执行实际删除。这三个阶段都有比较多的系统调用和文件系统操作。

*   rsync
    *   rsync所做的系统调用很少。
    *   没有针对单个文件做lstat和unlink操作。
    *   命令执行前期，rsync开启了一片共享内存，通过mmap方式加载目录信息。
    *   只做目录同步，不需要针对单个文件做unlink。

另外，在其他人的评测里，rm的上下文切换比较多，会造成System CPU占用较多——对于文件系统的操作，简单增加并发数并不总能提升操作速度。

### 总结

把文件系统的目录与书籍的目录做类比，rm删除内容时，将目录的每一个条目逐个删除(unlink)，需要循环重复操作很多次；rsync删除内容时，建立好新的空目录，替换掉老目录，基本没开销。

结论：频繁做减法不如直接从头来过。


---Why can rsync rapidly delete 400,000 files?--- 

---by 刘兰涛(http://blog.liulantao.com)---
