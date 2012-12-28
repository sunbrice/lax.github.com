---
layout: post
title: "Object C Programming Notes 2 - Defining a Class"
description: ""
categories: [Technology]
tags: [Object C, Mac OSX, Programming, Language]
---
{% include JB/setup %}

Class Interface
==========

    @interface ClassName : ItsSuperclass
    // Method and property declarations.
    @end

class methods, are preceded by a plus sign:

    + alloc;

instance methods, are marked with a minus sign:

    - (void)display;

Property declarations take the form:

    @property (attributes) Type propertyName;


#import is identical to #include, except that it makes sure that the same file is never included more than once.


[The Objective-C Programming Language](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocDefiningClasses.html)
