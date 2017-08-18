---
title: js设计模式笔记--简介
date: 2017-08-18 15:27:18
tags:
  - 设计模式
  - js基础
---

##创建函数比较多的时候应该加上命名空间
1. 可以写在一个对象里面，也可以用构造函数的方式
2. 链式调用就是返回一个拥有某个方法的对象
```flow js
'use strict'
// 1. 真假对象链式调用
var CheckObject = function () {
  var o = {
    checkName: function () {
      console.log('name')
      return o
    },
    checkEmail: function () {
      console.log('email')
      return o
    },
    checkPassword: function () {
      console.log('password')
      return o
    }
  }
  return o
}

var checkObject = CheckObject()
checkObject.checkName().checkEmail().checkPassword()

console.log('-----------------------addMethod-------------------------')

// addMethod添加多个函数
Function.prototype.addMethod = function (methods) {
  Object.keys(methods).forEach((key, index) => {
    this[key] = methods[key]
  })
  return this
}

var methods = function () {
}
methods
  .addMethod({
    'checkName': function () {
      console.log('name')
      return this
    },
    'checkPassword': function () {
      console.log('password')
      return this
    }
  })
  .addMethod({
    'checkEmail': function () {
      console.log('email')
      return this
    }
  })

methods.checkName().checkPassword().checkEmail()
```