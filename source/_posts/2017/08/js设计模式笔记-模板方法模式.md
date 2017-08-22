---
title: js设计模式笔记--模板方法模式
date: 2017-08-22 18:08:50
tags:
  - 设计模式
  - js基础
---

## 模板方法模式
1. 父类定义一组操作骨架，将一些具体的步骤放在子类中，使得子类不改变父类的情况下，实现多元化展现
  * 提供一个基础的弹框，外壳一样，但是里面的内容不一样，这就是模板方法的一种实现

<!-- more -->
```javascript
~function () {
  'use strict'
  function noop () {
  }
  /**
   * alert底层框架
   * @param data
   * @constructor
   */
  var Alert = function (data) {
    if (!data) return

    this.content = data.content
    this.panel = document.createElement('div')
    this.contentNode = document.createElement('p')
    this.confirmButton = document.createElement('span')
    this.closeBtn = document.createElement('b')
    this.panel.className = 'alert'
    this.closeBtn.className = 'a-close'
    this.confirmButton.className = 'a-confirm'
    this.confirmButton.innerHTML = data.confirm || '确认'
    this.contentNode.innerHTML = this.content
    this.success = data.success || noop
    this.fail = data.fail || noop
  }

  Alert.prototype = {
    init: function () {
      // 生成提示框
      this.panel.appendChild(this.closeBtn)
      this.panel.appendChild(this.contentNode)
      this.panel.appendChild(this.confirmButton)
      document.body.appendChild(this.panel)
      this.bindEvent()
      this.show()
    },
    bindEvent: function () {
      var _this = this
      this.closeBtn.onclick = function () {
        _this.fail()
        _this.hide()
      }
      this.confirmButton.onclick = function () {
        _this.success()
        _this.hide()
      }
    },
    hide: function () {
      this.panel.style.display = 'node'
    },
    show: function () {
      this.panel.style.display = 'block'
    }
  }

  // 右侧提示框实现
  var RightAlert = function (data) {
    Alert.call(this, data)
    this.confirmButton.className += ' right'
  }

  RightAlert.prototype = new Alert()

  // 标题提示框
  var TitleAlert = function (data) {
    Alert.call(this, data)
    this.title = data.title
    this.titleNode = document.createElement('h3')
    this.titleNode.innerHTML = this.title
  }

  TitleAlert.prototype = new Alert()

  TitleAlert.prototype.init = function () {
    this.panel.insertBefore(this.titleNode, this.panel.firstChild)
    Alert.prototype.init.call(this)
  }

  // 以基类的封装继续封装
  var CancelAlert = function (data) {
    TitleAlert.call(this, data)
    this.cancel = data.cancel
    this.cancelBtn = document.createElement('span')
    this.cancelBtn.className = 'cancel'
    this.cancelBtn.innerHTML = this.cancel || '取消'
  }

  // 这里方法重写基础行为，所有继承最基础的类
  CancelAlert.prototype = new Alert()

  CancelAlert.prototype.init = function () {
    TitleAlert.prototype.init.call(this)
    this.panel.appendChild(this.cancelBtn)
  }

  CancelAlert.prototype.bindEvent = function () {
    var _this = this
    TitleAlert.prototype.bindEvent.call(_this)
    this.cancelBtn.onclick = function () {
      _this.fail()
      _this.hide()
    }
  }

  // 使用
  new CancelAlert({
    title: 'dfasdfsdf',
    content: 'dsfsdfsdafs',
    success: function () {
      log('success')
    },
    fail: function () {
      log('fail')
    }
  }).init()

}()
```