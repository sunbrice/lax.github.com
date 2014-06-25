---
layout: page
title: Swift Quick Reference Guide
permalink: /SwiftQuickRef/
tags: [Swift, QuickRef]
page_hidden: true
document_tags: [Swift, QuickRef]
---

### Xcode6

#### 设置为默认开发工具

如果有多个版本，使用```xcode-select```将Xcode6设置为默认开发工具

    sudo xcode-select -s /Applications/Xcode6-Beta.app/Contents/Developer/

直接开启swift控制台（非playground）

    xcrun swift

### Swift基础语法

#### 常量与变量

声明常量：

    let myConstant = 10

读作```声明一个名为myConstant的常量，值为10```


声明变量：

    var myVariable = 0

读作```声明一个名为myVariable的变量，初值为0```

#### 类型声明

在常量或变量名后增加一个分号':'，一个空格' '，以及类型名。

    var welcomeMessage: String

读作```声明一个名为welcomeMessage的String类型变量```

如果声明时即为常量或变量赋值，则不必须加类型声明，因为Swift可以推断出合适的类型。

#### If条件中使用Forced Unwrapping

`Optional`类型可以用在`if`语句中作为条件，如果数据为`nil`，返回`false`；如果数据非空，返回`true`。

    var myvariable1: Int? = 1

    if myvariable1 {
      println("myvariable1 is \(myvariable1)")
    } else {
      println("oops")
    }

#### If条件中使用Optional Binding

与`Forced Unwrapping`不同，`Optional Binding`方式可以判断常量或变量是否为`nil`，如非空，则赋值给一个临时变量或临时常量。


临时常量。

    var myvariable2: Int? = 1
    if let pp = myvariable2 {
      println("myvariable2 is \(myvariable2), pp is \(pp)")
    } else {
      println("oops")
    }

临时变量，变量可以被改写。

    var myvariable3: Int? = 1
    if var pp = myvariable3 {
      pp = pp * 2
      println("myvariable3 is \(myvariable3), pp is \(pp)")
    } else {
      println("oops")
    }


### UI

#### UIAlertView

    let alert: UIAlertView = UIAlertView()
    alert.title = "This a simple UIAlertView"
    alert.message = "Press OK to continue"
    alert.addButtonWithTitle("OK")
    alert.show()
