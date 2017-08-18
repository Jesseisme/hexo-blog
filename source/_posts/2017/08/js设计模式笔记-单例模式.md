---
title: js设计模式笔记--单例模式
date: 2017-08-18 15:38:50
tags:
  - 设计模式
  - js基础
---

## 单例模式
* 只能实例化一次，第二次实例化将之前实例化好的对象返回，就可以共享实例的内存，可用于modal弹框

```flow js
'use strict'
var single = (function () {
  var instance = null

  function Fuck (bitch) {
    this.bitch = bitch
  }

  Fuck.prototype.start = function () {
    return this.bitch
  }

  return {
    getInstance: function (bitch) {
      if (!instance) {
        instance = new Fuck(bitch)
      }
      return instance
    }
  }
})()

// 共享一个实例
var s = single.getInstance('fucker')
console.log(s)
var y = single.getInstance('dsfsd')
console.log(y)
console.log(y.start())
```