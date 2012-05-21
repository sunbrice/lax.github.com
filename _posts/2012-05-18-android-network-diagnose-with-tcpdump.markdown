---
layout: post
name: android-network-diagnose-with-tcpdump
title: Android网络问题检查（使用TCPDUMP）
time: 2012-05-18 19:00:00.000000000 +08:00
category:
- Technology
tags:
- android
- tcpdump
---
随着智能手机的增长，智能平台（Andorid，iOS）的装机量也在飞速增加。为开发者带来很大商机。

开发者对android平台的重视，意味着支持部门也要与这个系统打更多的交道，技术人员需要将已有的经验迁移到新的平台中。在传统的PC web中，网络相关问题已经有比较完善的方法和流程来进行定位和追踪，而移动设备端的相关软件还需要进行整合与优化。

最近遇到开发人员报告的问题，需要确定问题发生时网络通讯是否正常。为此测试设计了两种方案，分别用模拟器和实际设备配合网络工具进行网络状况调试和分析。

## 方案一 使用Android SDK包含的模拟器进行调试

### 准备Android SDK环境

从Android开发者网站下载相应开发平台的[Android SDK](http://developer.android.com/sdk/index.html)，我下载了Linux版SDK，版本为[r18](http://dl.google.com/android/android-sdk_r18-linux.tgz)。

如果是Windows平台，下载安装包直接双击即可，可以跳过本段。如果你和我一样用Linux，可按以下操作。

以下假设安装目录为~/apps/，请根据情况创建目录或修改命令参数。

解压

    tar zxvf /path/to/android-sdk_r18-linux.tgz

将解压的文件移动到目标目录

    mv android-sdk-linux/ ~/apps/android-sdk-linux

为方便使用，配置环境变量。在~/.bashrc中增加以下内容。

    # Android SDK
    ADT=$HOME/apps/android-sdk-linux
    PATH=${PATH}:${ADT}/tools:${ADT}/platform-tools

退出当前终端并重新登陆，或执行以下命令。

    source ~/.bashrc


### 用SDK安装所需的虚拟镜像
执行以下命令，带开Android SDK Manager。

    android

选择安装Android SDK Tools和Android SDK Platfor-tools，选择安装Android 2.3.3下的SDK Platform和Google APIs。

### 创建虚拟设备
执行以下命令，带开Android Virtual Device Manager。

    android avd

点击New，开始新建设备。输入设备名称为android，Target选择2.3.3，SD卡大小设置为512MiB。

创建完毕。

### 启动虚拟设备和相关工具，安装apk

    emulator -netfast -debug proxy @android

    sudo `which adb` start-server

    adb install xxx.apk

### 开始调试

在虚拟设备界面中选择安装的软件，进行应用的操作。

<s>由于我们在启动模拟器时，指定了调试网络代理，因此操作软件时的网络交互可以在模拟器的终端下看到调试信息。</s> ---实际测试时使用了squid作为代理。重复测试发现没有重现输出内容，需要验证。



## 方案二 使用TCPDUMP进行实机测试

准备Android环境的方法前面已经介绍。

### 设备准备

原则上任何安装Android系统的手机、平板电脑都可以进行此次调试。由于本次测试需求，需要ROOT并开启开发者模式。操作方法在此不再做介绍（网上太多相关文章了）。

我测试用的是一台HP TouchPad，安装CM9。

### 软件

下载[tcpdump-arm](http://www.eecs.umich.edu/~timuralp/tcpdump-arm)

将tcpdump-arm传到设备上

    adb push tcpdump-arm /data/local/

登入设备，设置文件权限

    adb shell

    cd /data/local/

    chmod 777 tcpdump-arm

安装程序包

    adb install xxx.apk

### 开始调试

在虚拟设备界面中选择安装的软件，进行应用的操作。

仍然使用电脑进入adb shell中，启动tcpdump-arm，开始抓取数据包。

    ./tcpdump-arm -s 1500 -n -w result.cap

以上命令使用-w参数，将抓包结果保存到文件，方便后续进行进一步分析。

从设备中把结果文件复制出来

    adb pull /data/local/result.cap ./

使用wireshark查看

    wireshark result.cap
