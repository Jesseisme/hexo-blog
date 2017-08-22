---
title: js设计模式笔记--建造者模式
date: 2017-08-18 15:36:51
tags:
  - 设计模式
  - js基础
---

## 建造者模式
* 创建出来的对象直接具有已实现的某些属性方法
* 此模式参与对象的创建过程，干涉了对象的创建细节
* 场景：已有内部给定的方法和属性，只需要选择需要的属性，就可以产生一个完整的对象

```
~function () {
  'use strict'

  var Human = function (param) {
    this.skill = param && param.skill || '保密'
    this.hobby = param && param.hobby || '保密'
  }

  Human.prototype = {
    getSkill: function () {
      return this.skill
    },
    getHobby: function () {
      return this.hobby
    }
  }

  var Named = function (name) {
    var _this = this
    // 构造
    ~function (name, _this) {
      _this.wholeName = name
      if (name.indexOf(' ') > -1) {
        _this.firstName = name.slice(0, name.indexOf(' '))
        _this.secondName = name.slice(name.indexOf(' '))
      }
    }(name, _this)

  }

  var Work = function (work) {
    var _this = this
    ~function (work, _this) {
      switch (work) {
        case 'code':
          _this.work = '工程师'
          _this.workDescript = '每天沉醉于编程'
          break
        case 'UI':
        case 'UE':
          _this.work = '设计师'
          _this.workDescript = '设计更似一种艺术'
          break
        case 'teach':
          _this.work = '教师'
          _this.workDescript = '分享也是一种快乐'
          break
        default:
          _this.work = work
          _this.workDescript = '不清楚你的职业'
      }
    }(work, _this)
  }

  Work.prototype.changeWork = function (work) {
    this.work = work
  }

  Work.prototype.changeDescript = function (descript) {
    this.workDescript = descript
  }

  var Person = function (name, work) {
    var _person = new Human()
    _person.name = new Named(name)
    _person.work = new Work(work)

    return _person
  }

  var person = new Person('s b', 'UI')

  console.log(person.skill)
  console.log(person.name.firstName)

}()

```