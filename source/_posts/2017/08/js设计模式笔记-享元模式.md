---
title: js设计模式笔记--享元模式
date: 2017-08-22 13:23:16
tags:
  - 设计模式
  - js基础
---

## 享元模式
1. 运用共享技术有效的支持最大量的细粒度的对象，避免对象间拥有相同的内容造成多余的开销
2. 主要对数据和方法共享分离，将数据和方法分为外部和内部，内部就是共享的，所以提出来这一部分能提高性能
3. barrat李靖的解释
  * “享”是共享的意思，“元”指的是元件，也就是小颗粒的东西，
     享元顾名思义便是共享小部件，很多系统或者程序包含大量对象，但是这些对象
     绝大多数都是差不多的，除了一些极个别的属性外。
     在享元模式中有两个比较重要的关键词，内部变量和外部变量；内部变量是可以
     共享的属性集，而外部变量是对象之间的差异部分，通过相同+不同的方式组合诸
     多对象，可以有效地节省系统空间，降低内存大小。

<!-- more -->

```
~function () {
  'use strict'
  var Flywight = function () {
    var created = []

    function create () {
      var dom = document.createElement('div')
      document.getElementById('container').appendChild(dom)
      created.push(dom)
      return dom
    }

    return {
      getDiv: function () {
        if (created.length < 5) {
          return create()
        } else {
          var div = created.shift()
          created.push(div)
          return div
        }
      }
    }

  }

  var paper = 0,
    article = ['fsdfsdafsadf', 'fsdfsdfsdfdsa', 'fdsfsdfsdfasdf', 'fsdfsdfsdafsdafsdaf', 'ffsdfsdfsdafsdafasdfasdf'],
    num = 5,
    len = article.length

  for (var i = 0; i < 5; i++) {
    if (article[i]) {
      Flywight.getDiv().innerHTML = article[i]
    }
  }

  document.getElementById('next_page').onclick = function () {
    if (article.length < 5) {
      return
    }

    var n = ++paper * num % len,
      j = 0

    for (; i < 5; i++) {
      if (article[n + j]) {
        Flywight.getDiv().innerHTML = article[n + j]
      } else if (article[n + j - len]) {
        Flywight.getDiv().innerHTML = article[n + j - len]
      } else {
        Flywight.getDiv().innerHTML = ""
      }
    }
  }

}()

~function () {
  // 享元方法，其实也就是共享方法
  'use strict'
  var FlyWeight = {
    moveX: function (x) {
      this.x = x
    },
    moveY: function (y) {
      this.y = y
    }
  }

  var Player = function (x, y, c) {
    this.x = x
    this.y = y
    this.color = c
  }

  Player.prototype = FlyWeight
  Player.prototype.changeC = function (c) {
    this.color = c
  }

}()
```