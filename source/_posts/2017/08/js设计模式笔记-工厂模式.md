---
title: js设计模式笔记--工厂模式
date: 2017-08-18 15:33:04
tags:
  - 设计模式
  - js基础
---
## 工厂模式
1. 工厂模式顾名思义就是一个工厂，里面可以创建各种类型的产品
2. 工厂模式和类
  * 工厂模式可以根据所需产生不同的类
  * 类是一个厂内一个小型的团体
  
3. 抽象工程模式
  * 父类不提供实体方法，只提供接口，通过子类去实现覆盖

```flow js
'use strict'
const log = console.log.bind(console)

log('--------简单工厂，就是一个简单的封装--------')

~function () {
  function Button (type, placeholder) {
    let o = Object.create(null)
    o.content = placeholder
    o.click = function () {
      log(placeholder)
    }

    if (type === 'error') {
      o.color = 'red'
    } else {
      o.color = 'blue'
    }

    return o
  }

  var errorBtn = Button('error', 'heheheheh')
  var primaryBtn = Button('df', 'dfsdfsdfsdfd')
  log(errorBtn.content)
  log(primaryBtn.content)
}()

log('-------工厂方法模式---------')

~function () {
  function Button (type, content) {
    if (this instanceof Button) {
      var o = new this[type](content)
      return o
    } else {
      return new Button(type, content)
    }
  }

  Button.click = function (content) {
    log(content)
  }

  Button.prototype = {
    constructor: 'Button',
    error: function (content) {
      this.color = 'red'
      Button.click(content)
    },
    primary: function (content) {
      this.color = 'blue'
      Button.click(content)
    }
  }

  var a = Button('error', 'content')
  log(a.color)

}()
```