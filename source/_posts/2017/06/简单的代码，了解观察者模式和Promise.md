---
title: 简单的代码，读懂观察者模式和Promise
date: 2017-06-10 20:10:35
tags:
  - javascript
  - 基础知识
---

## 观察者模式
### 观察者模式又叫做发布订阅模式，其基础支撑是事件的发布与订阅。
-- 基于观察者模式，可以做数据的绑定，代码的解耦。
<!-- more -->

##### 一个简单的观察者模式代码示例：
```javascript
function Observer() {
    // 容器，存放subscribe订阅的内容
    this.subscribes = {
    }
}

Observer.prototype = {
    constructor: 'Observer',
    // 往容器放东西
    subscribe: function (eventName, callback) {
        this.subscribes[eventName] = callback
    },
    // 取函数，执行
    publish: function (eventName, stuff) {
        // 把订阅的东西取出来
        // 执行
        this.subscribes[eventName](stuff)
    }
}
```
##### 使用此代码
```javascript
var observer = new Observer()

observer.subscribe('吃饭', function (stuff) {
    console.log(stuff) // 成功打印面条
})

setTimeout(function () {
    observer.publish('吃饭', '面条')
}, 2000)
```

##### 代码的逻辑很简单
 1. **subscribe**的时候将要执行的函数放入存放函数的容器
 2. **publish**的时候根据事件的名称取出函数，并传入数据，执行
 
## Promise
### Promise是js异步的解决方案，通常用于ajax请求
#### 继续编写简单的代码
```javascript
function Promise(executor) {
    // 容器，存放then订阅的东西
    this._deferreds = []
    var _this = this
    // 承诺被执行
    function resolve(stuff) {
        // 取出存的东西
        _this._deferreds.forEach(function (deferred) {
            // 执行
            deferred(stuff)
        })
    }
    // Promise传入的函数，执行时将resolve传进去
    executor(resolve)
}

Promise.prototype.then = function (onFulfilled) {
    // 往里面放东西
    this._deferreds.push(onFulfilled)
}
```
##### 使用此代码
```javascript
new Promise(function (resolve) {
    setTimeout(function () {
        resolve('apple')
    }, 2000)
})
    .then(function (stuff) {
        console.log(stuff) // apple
    })
```
##### Promise的关键点在于
1. **.then**是给存放回调的容器里面添加回调函数
2. **resolve**是**then**注册回调的执行者，所以当异步的时候，then要后于resolve执行。同样PubSub也可以用于异步
3. Promise也是观察者模式的一种实现
```javascript
// 使用事件发布订阅处理异步
var observer = new Observer()

observer.subscribe('吃饭', function (stuff) {
    console.log(stuff) // 面条
})

$.ajax({
   url: '饭堂'
})
.success(function(data) {
  observer.publish('吃饭', data['面条'])
})
```
##### 都可以轻松的实现异步的解耦

### 以上都是最简单的实现，二八定律中的二吧，有兴趣可以用es6写一个简单版的Promise
[es6Promise](http://faceplus.top/2017/04/18/es6%E7%89%88promise/)
