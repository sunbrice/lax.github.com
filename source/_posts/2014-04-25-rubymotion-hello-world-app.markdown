---
layout: post
title: "使用RubyMotion 5分钟开发iOS应用"
date: 2014-04-25 13:53:49 +0800
comments: true
categories: 
---

## 背景
这是本人在iOS/RubyMotion开发方面的第一篇文档，作为对相关工具链的经验记录。

我对各种新奇技术一直保持一定的兴趣，而不仅局限于工作相关的领域。移动领域在很大程度上影响了人们生活的习惯，从2012开始关注iOS开发，并且玩票做过一个简单的[工具应用](https://itunes.apple.com/us/app/bei-shi-da-ren-zheng-wang-guan/id591059829?ls=1&mt=8)。

2013年发现了RubyMotion这个神器并持续关注，看到RM的工具链逐渐走向成熟的过程，社区也逐步壮大，目前基本进入了一个完善且稳定发展的状态。

## 关于RubyMotion（RM）

### 优势

RubyMotion是一个能帮助你使用Ruby语言来替代Objective-C来开发iOS平台及OSX平台应用的工具。Ruby语言相对与Objective-C的优势是较少的代码量键入，以及动态类型系统。
如：

    UILabel *label = [[UILabel alloc] init];

完全等价于

    label = UILabel.alloc.init

经过RubyMotion改进后，可以使用与Ruby常用方式相同的写法：

    label = UILabel.new

### 底层实现

这个工具在语言层面与MacRuby一脉相承，通过将Ruby代码编译为与Objective-C相同的LLVM底层代码，实现了与Objective-C/Cocoa框架的交互与统一。Cocoa的类与Ruby的类实现了对应，代码中消息传递的目标仍然是Objective-C的对象，Apple官方文档的实例代码可以经过简单的变换即可直接使用。

采用这种方式的另一个好处是不需要维护庞大的基础代码，可以支持Apple SDK的升级。


## 开发环境准备

开发iOS App的传统工具基本都包含在Xcode套件中，包含编译器，代码编辑工具，界面工具，iOS模拟器，发布工具等。理论上使用RubyMotion只需要有编译器和模拟器即可进行开发阶段的工作。考虑到采用Xcode中优秀的工具如Interface Builder，更好的选择是仍然安装完整Xcode套件。

Ruby运行环境。RubyMotion用到了rake等工具，推荐使用rvm来安装ruby环境，建议安装ruby-2.0.0之后的版本。（如果你用过cocoapods，可能已经安装过了）。

    \curl -sSL https://get.rvm.io | bash -s stable
    source /etc/profile
    
    rvm install ruby-2

在RubyMotion官网购买后，你会收到证书和安装文件的下载链接。按提示安装后，可以在Shell中使用motion命令。

之后大部分操作都在shell中使用motion命令及rake命令。

## 开始代码相关

### 初识motion命令

执行一条命令即可创建一个工程的代码框架。

    $ motion create HelloWorld
    Create HelloWorld
    Create HelloWorld/.gitignore
    Create HelloWorld/app/app_delegate.rb
    Create HelloWorld/Gemfile
    Create HelloWorld/Rakefile
    Create HelloWorld/resources/Default-568h@2x.png
    Create HelloWorld/spec/main_spec.rb

创建的文件能够在输出结果看到。

进入代码目录，初始化ruby工具：

    ➜  ~ $  cd HelloWorld
    ➜  HelloWorld $  bundle
    Fetching gem metadata from https://rubygems.org/..
    Resolving dependencies...
    Using rake (10.3.1) 
    Using bundler (1.3.5) 
    Your bundle is complete!
    Use `bundle show [gemname]` to see where a bundled gem is installed.

运行这个项目

    ➜  HelloWorld  rake
         Build ./build/iPhoneSimulator-7.1-Development
       Compile ./app/app_delegate.rb
        Create ./build/iPhoneSimulator-7.1-Development/HelloWorld.app
          Link ./build/iPhoneSimulator-7.1-Development/HelloWorld.app/HelloWorld
        Create ./build/iPhoneSimulator-7.1-Development/HelloWorld.app/PkgInfo
        Create ./build/iPhoneSimulator-7.1-Development/HelloWorld.app/Info.plist
          Copy ./resources/Default-568h@2x.png
        Create ./build/iPhoneSimulator-7.1-Development/HelloWorld.dSYM
      Simulate ./build/iPhoneSimulator-7.1-Development/HelloWorld.app
    (main)>    



第一次运行会编译项目的代码，需要等待一段时间。之后能看到iOS模拟器启动。

这时候的应用只是一个黑色的屏幕，没有任何文字说明和页面提示。Shell此时进入[交互模式](http://www.rubymotion.com/developer-center/articles/debugging/)。

怎么样，这种感觉，有没有想到初恋的Rails？

### Say "Hello World!"

注：本节参考[RubyMotion Tutorial的例子](http://rubymotion-tutorial.com/1-hello-motion/)

修改```./app/app_delegate.rb```，修改后的相关内容如下。

    class AppDelegate
      def application(application, didFinishLaunchingWithOptions:launchOptions)
        alert = UIAlertView.new
        alert.message = "Hello World!"
        alert.show
    
        puts "Hello World, Again!"
    
        true
      end
    end

App初始化时创建一个UIAlertView对象，之后显示出来。

重新执行```rake```命令，这次可以看到界面中间的提示消息。

![RubyMotion Hello World](/images/2014/rubymotion-hello-world-1-alert.png)

从交互窗口可以看到输出的消息```Hello World, Again!```。

    ➜  HelloWorld  rake
         Build ./build/iPhoneSimulator-7.1-Development
        Create ./build/iPhoneSimulator-7.1-Development/HelloWorld.app/Info.plist
      Simulate ./build/iPhoneSimulator-7.1-Development/HelloWorld.app
    (main)> Hello World, Again!
    (main)> 

这个例子使用了Cocoa的功能（UIAlertView），实现了基本的文字展示，并且还有控制台的文本输出——使用puts进行基本的辅助调试。

既然是Hello World，细节就不解释太多了。以后的文章会关注更多方面。

## 总结
RubyMotion已经形成一个基本的生态圈，进行App开发的上手速度也远远超过了传统工具；配合Xcode工具链使用，大大提高开发速度，能改善代码可维护性。

## Reference

1. [RubyMotion Official](http://www.rubymotion.com)
* [RubyMotion Tutorial](http://rubymotion-tutorial.com)
* [RubyMotion on Quora](http://www.quora.com/RubyMotion)
