---
date: '2011-09-27 14:47:29'
layout: post
slug: about-common-lisp
status: publish
title: about common lisp
wordpress_id: '106'
categories:
- Technology
tags:
- common lisp
- eval
- lisp
- Paul Graham
- polish notation
- ruby
---



一个偶然的机会，看到[田春](http://tianchunbinghe.blog.163.com/)正在翻译[Practical Common Lisp](http://www.gigamonkeys.com/book/)——前一天刚看到这本书，还苦苦找了它的中译版而未得。

说说Common Lisp（常被简称CL）。CL是lisp的一种实现，代码和数据都是数学符号的模样——采用polish notation。

如加法表达式：

    
    (+ 2 2)


再如定义一个函数：

    
    (defun square (x) (* x x))


作为一种符号语言，它有坚固的理论基础。CL强大的表达能力和灵活的语言特性，成为被很多技术牛人吹捧的原因。最著名的推广者是[Paul Graham](http://en.wikipedia.org/wiki/Paul_Graham)——《黑客与画家》等书的作者。

CL的另一个强大之处在于eval，这个功能将一段数据变成可执行的操作，实现了数据与函数的统一。eval对程序开发带来深远影响，之后一些其它语言也有了相似的功能实现，比如Ruby中的实现







    
    a = 1
    eval('a + 1') #  (evaluates to 2)
    
    # evaluating within a context
    def get_binding(a)
      binding
    end
    eval('a+1',get_binding(3)) # (evaluates to 4, because 'a' in the context of get_binding is 3)















    
    class Test; end
    Test.class_eval("def hello; return 'hello';end") # add a method 'hello' to this class
    Test.new.hello                    # evaluates to "hello"








太灵活了，有木有？！

作为程序员的一门最佳练级语言，CL值得一看。


