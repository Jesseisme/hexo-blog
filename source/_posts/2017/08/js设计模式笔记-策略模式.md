---
title: js设计模式笔记--策略模式
date: 2017-08-22 18:13:14
tags:
  - 设计模式
  - js基础
---
## 策略模式
1. 将定义的一组算法封装起来，使其可以相互替换。封装的算法彼此之间独立，不会随着客户端的变化而变化
  * 通过定义算法返回结果，和状态模式不同的是，没有对象的依赖

<!-- more -->

```javascript
~function () {
  'use strict'
  // 实现
  var PriceStrategy = (function () {
    var strategy = {
      return30: function (price) {
        return +price + parseInt(price / 100) * 30
      },
      return50: function (price) {
        return +price + parseInt(price / 100) * 50
      },
      percent90: function (price) {
        return price * 100 * 90 / 10000
      },
      percent80: function (price) {
        return price * 100 * 80 / 10000
      },
      percent50: function (price) {
        return price * 100 * 50 / 10000
      }
    }
    function doStrategy (algorithm, price) {
      return strategy[algorithm] && strategy[algorithm](price)
    }
    doStrategy.addStrategy = function (field, fn) {
      strategy[field] = fn
    }
    return doStrategy
  })()

  // 使用
  var price = PriceStrategy('return50', '346.5')
  console.log(price)

  // jQuery的animate就是使用策略模式实现的，传入不同的参数，产生不一样的效果
  // $('div').animate({ width: '200px' }, 1000, 'linear')
  // $('div').animate({ width: '200px' }, 1000, 'swing')

  // 在这个时候需要打6折怎么办？直接去改的话太麻烦,加上addStrategy方法
  // 执行
  var p = PriceStrategy('percent80', '34343')
  console.log(p)

  PriceStrategy.addStrategy('test59', function (price) {
    return price * 1000000
  })

  var p = PriceStrategy('test59', '3434')
  console.log(p)

}()
```