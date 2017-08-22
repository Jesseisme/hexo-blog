---
title: js设计模式笔记--抽象工厂模式
date: 2017-08-18 15:35:43
tags:
  - 设计模式
  - js基础
---

## 抽象工厂模式
1. 抽象类：声明但是不能使用的类
2. 创造一个通用的大类，通过这个类去创建一系列抽象类，让子类去继承父类定义的但不能使用的方法,子类然后去实现这个抽象的方法
```
~function (global) {
  'use strict'
  var Car = function () {
  }
  Car.prototype = {
    getPrice: function () {
      return new Error('抽象方法不能调用')
    },
    getSpeed: function () {
      return new Error('抽象方法不能调用')
    }
  }

  // 抽象工厂方法
  var VehicleFactory = function (subType, superType) {
    if (typeof VehicleFactory[superType] === 'function') {
      // noinspection JSAnnotator
      function F () {
      }

      F.prototype = new VehicleFactory[superType]()
      subType.constructor = subType
      // 子类原型继承父类
      subType.prototype = new F()
    } else {
      throw new Error('未创建该抽象对象')
    }
  }

  VehicleFactory.Car = function () {
    this.type = 'car'
  }

  VehicleFactory.Car.prototype = {
    getPrice: function () {
      return new Error('抽象方法不能调用')
    },
    getSpeed: function () {
      return new Error('抽象方法不能调用')
    }
  }

  VehicleFactory.Bus = function () {
    this.type = 'bus'
  }

  VehicleFactory.Bus.prototype = {
    getPrice: function () {
      return new Error('抽象方法不能调用')
    },
    getSpeed: function () {
      return new Error('抽象方法不能调用')
    }
  }

  VehicleFactory.Truck = function () {
    this.type = 'truck'
  }

  VehicleFactory.Truck.prototype = {
    getPrice: function () {
      return new Error('抽象方法不能调用')
    },
    getSpeed: function () {
      return new Error('抽象方法不能调用')
    }
  }

  var BMW = function (price, speed) {
    this.price = price
    this.speed = speed
  }

  VehicleFactory(BMW, 'car')

  BMW.prototype.getPrice = function () {
    return this.price
  }

  BMW.prototype.getSpeed = function () {
    return this.speed
  }

}(this)
```