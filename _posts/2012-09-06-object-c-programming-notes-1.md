---
layout: post
title: "Object C Programming Notes 1 - Objects, Classes and Messaging"
description: ""
categories: [Technology]
tags: [Object C, Mac OSX, Programming, Language]
---
{% include JB/setup %}


消息机制
----------

类似函数调用，Object C中采用消息机制，基本调用语法如下：    

    [receiver message]

接收方receiver是一个对象，传递的消息通知该对象进行操作的方法名。消息传递的方法名经常叫做selector。  

可以使用点(.)操作符访问对象的accessor方法。

给nil发消息是允许的——在运行时它仅仅是没有任何效果：消息发送给nil并返回nil。

消息传递的方法对接收方的实例变量有访问权限。

多态：不同类对象有相同的方法。


方法调用与消息的区别
----------

方法调用：在编译阶段就已确定其方法和参数；  

消息：运行阶段消息发送时才有运行时环境确定。Dynamic binding / Dynamic Method Resolution


Dot Syntax
----------

点（.）操作符与消息的使用  
    myInstance.value = 10;
    [myInstance setValue:10];


NSObject
==========

继承(Inheriting Methods)

覆盖继承来的方法，但是不能覆盖继承来的变量（因为继承时内存已分配）。

抽象类（Abstract Classes）


Class Types
==========

静态类型Static Typing
----------

编译器可以做类型检查


内省Type Introspection
----------
运行时获知类型等信息。


Class Objects
==========

A class definition contains various kinds of information, much of it about instances of the class:  
-    The name of the class and its superclass
-    A template describing a set of instance variables
-    The declarations of method names and their return and parameter types
-    The method implementations


Class Methods vs. Instance Methods
----------

    int versionNumber = [Rectangle version];

    id aClass = [anObject class];
    id rectClass = [Rectangle class];

    Class aClass = [anObject class];
    Class rectClass = [Rectangle class];

Cloass Objects的类型都是Class。


创建实例
==========

    id  myRectangle;

    myRectangle = [Rectangle alloc];

初始化实例状态  
    myRectangle = [[Rectangle alloc] init];

变量


[The Objective-C Programming Language](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocObjectsClasses.html)
