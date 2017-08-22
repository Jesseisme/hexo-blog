---
title: js设计模式笔记--装饰者模式
date: 2017-08-18 15:44:41
tags:
  - 设计模式
  - js基础
---
##装饰者模式
* 不改变原对象的基础上，通过添加属性或方法使原对象满足要求
```
'use strict'
// 装饰者
var decorator = (function () {
  return function (input, fn) {
    // 获取事件源
    var input = document.getElementById(input)
    if (typeof input.onclick === 'function') {
      // 将之前的存起来，那之前有很多怎么办
      var old = input.onclick
      input.onclick = function () {
        old()
        // 看见吗就是执行了之前的函数，顺便加了个回调
        fn()
      }
    } else {
      input.onclick = fn
    }
    // do something

  }
}())

document.getElementById('btn').onclick = function() {
  console.log('fuck')
}

decorator('btn', function() {
  console.log('just go on')
})
```