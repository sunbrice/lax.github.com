---
layout: post
slug: ruby-c-cxx-extention
status: publish
title: 在Ruby中利用C/C++代码：为Ruby开发扩展
date: '2012-02-25 00:06:29'
wordpress_id: '180'
categories: [Technology, Ruby]
tags:
- C++
- ruby
---
在一个程序里使用多种语言进行开发，出发点有若干种：


+   为了性能，嵌入低级语言的代码进行优化。
+   为了开发效率，嵌入高级语言减少代码行数。
+   重用一些成熟的工具和库文件。
+   DSL简化配置，降低维护成本。


最近因为要重用一段C++代码，遇到了原来代码逻辑比较复杂的问题。新项目是一段测试程序，首选是一种解释语言。想在脚本中重用这些功能，完全重写的代价比较高，需要花费精力理清原来程序的逻辑。好在输入和输出的内容都很简单，而Ruby提供了可以方便利用的接口，写一个功能扩展来调用旧的C++代码似乎是一个说得过去的选择。

C++的接口为：

    std::string PrivateUtil::getInfo(std::string);

首先创建一个文件private_util.cc，包含头文件ruby.h

    #include <ruby.h>
    #include "album_util.h"

然后声明所有要实现的功能，与ruby脚本通讯：

    VALUE getInfo(VALUE input_str);

下一步，开始进入模块功能注册，也是这个文件的核心。定义一个Ruby的Module，名字为PrivateUtil；另外定义一个getInfo的方法，并且用刚才声明的getInfo与之对应。

    extern "C" void Init_foo()
    {
        VALUE private_util = rb_define_module("PrivateUtil");
        rb_define_module_function(private_util, "getInfo", RUBY_METHOD_FUNC(getInfo), 2);
    }

现在可以去实现getInfo的具体逻辑了。getInfo的实现也很简单，调用了旧代码的接口，返回一个ruby_str即可。这个返回值需要是ruby的数据类型，以便在脚本中直接使用。

    VALUE getInfo(VALUE input_str)
    {
        return ruby_str_new2(PrivateUtil::getInfo(input_str));
    }

到此步骤，c++文件基本完成。

下面开始，建立extconf.rb

    require 'mkmf'
    $libs = '-lstdc++'
    create_makefile 'private_util'

执行

    ruby extconf.rb && make

自动生成一个Makefile文件。make后生成了可供ruby脚本调用的so文件：private_util.so。

直接在ruby中调用private_util:

    irb > require 'private_util'
    true
    irb > PrivateUtils::getInfo("abc")
    "abc"

如果想在系统全局使用，可以执行make install，这样就能在其它脚本里方面调用。

欢迎交流使用心得。http://www.liulantao.com/
