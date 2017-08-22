---
title: js设计模式笔记--状态模式
date: 2017-08-22 18:11:59
tags:
  - 设计模式
  - js基础
---
## 状态模式
1. 当一个对象的内部状态发生改变时，会导致其行为的改变，看起来像是改变了对象
  * 当碰见需要多次判断某一个值得时候，不能确定是否还是会继续添加，就用状态模式，将判断的条件当做对象内部的一种状态

<!-- more -->
```javascript
~function () {
  'use strict'
  // 实现
  var log = console.log.bind(console)
  var ResultState = function () {
    var States = {
      state0: function () {
        log('这是第一种情况')
      },
      state1: function () {
        log('这是第二种情况')
      },
      state2: function () {
        log('这是第三种情况')
      },
      state3: function () {
        log('这是第四种情况')
      }
    }

    function show (result) {
      States['state' + result] && States['state' + result]()
    }

    return {
      show
    }
  }
  // 调用
  ResultState().show(1)
  ResultState().show(2)

  /**
   * 模仿游戏的人物，执行的动作可以看做一种状态，游戏中的人物有时候需要连续执行多个动作
   * @constructor
   */
  var MarryState = function () {
    var _currentState = {},
      states = {
        jump: function () {
          log('jump')
        },
        move: function () {
          log('move')
        },
        shoot: function () {
          log('shoot')
        },
        squat: function () {
          log('squat')
        }
      }

    var Action = {
      changeState: function () {
        var args = [].slice.call(arguments)
        _currentState = {}
        if (args.length) {
          args.forEach(arg => {
            _currentState[arg] = true
          })
        }
        return this
      },
      goes: function () {
        log('触发一次动作')
        for (let i in _currentState) {
          states[i] && states[i]()
        }
        return this
      }
    }
    return {
      change: Action.changeState,
      goes: Action.goes
    }

  }

  var marry = new MarryState()

  marry
    .change('jump', 'shoot')
    .goes()
    .goes()
    .change('shoot')
    .goes()


}()
```