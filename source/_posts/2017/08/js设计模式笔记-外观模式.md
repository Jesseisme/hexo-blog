---
title: js设计模式笔记--外观模式
date: 2017-08-18 15:40:23
tags:
  - 设计模式
  - js基础
---
## 外观模式
*  这章坑爹，就是说了下兼容性的封装，在写个小小型代码库

```flow js
'use strict'
var A = (function () {
  var o = {
    g: function (id) {
      return document.getElementById(id)
    }
  }
  // todo: 其余不想写了，就是获取元素，添加事件的封装
  return o
}())
```