---
title: js设计模式笔记--观察者模式
date: 2017-08-22 18:10:32
tags:
  - 设计模式
  - js基础
---

## 观察者模式
1. 观察者模式又被称作发布-订阅模式，是一种消息机制，依靠事件很容易实现解耦
 * 观察者模式称为js最重要的设计模式都不为过
 * 观察者模式实际是函数的回调，先将函数放入（订阅）容器中，发布的时候从容器中拿出执行

<!-- more -->
```javascript
~function () {
  'use strict'
  var log = console.log.bind(console)
  // 观察者模式实现
  var Observer = (function () {
    var _messages = {}
    return {
      regist: function (type, fn) {
        if (!_messages.hasOwnProperty(type)) {
          _messages[type] = [fn]
        } else {
          [].push.call(_messages[type], fn)
        }
      },
      fire: function (type, args) {
        if (!_messages[type]) return
        var events = {
          type: type,
          data: args || {}
        }
        _messages[type].forEach(message => {
          message.call(this, events.data)
        })
      },
      remove: function (type, fn) {
        if (_messages[type] instanceof Array) {
          var i = _messages[type].length - 1
          for (; i >= 0; i--) {
            _messages[type][i] === fn && _messages[type].splice(i, 1)
          }
        }
      }
    }
  })
  var observer = Observer()
  var fn = function (bitch) {
    log('fuck ', bitch)
  }
  // 使用
  observer.regist('fuck', fn)
  observer.regist('gun', fn)

  observer.fire('fuck', '韩梅梅')

  observer.remove('fuck', fn)

  observer.fire('fuck', 'dsfdsf')

  observer.fire('gun', 'fuck')

}()
```