---
title: es6版promise
categories: 小轮子
tags: 
  - javascript
  - es6
  - promise
---

## es6版Promise
#### 用法
```
  new Promise(function(resolve, reject) {
    resolve('resolve')
  })
    .then(function(result) {
      console.log(result) // resolve
    })
```
#### ``Promise``是一个构造函数，接受一个函数作为参数  
  这个函数接受两个参数：  
  (1) resolve 成功的时候返回的数据 （履行承诺）  
  (2) reject 失败的时候返回的数据 （拒绝承诺）
#### ``Promise``原型方法
  (1) ``then``注册成功后的回调  
  (2) ``catch``注册失败后的回调  
<!-- more -->
#### 上源代码——由于使用es6所以用babel，[点击看编译过的代码](https://github.com/slipkinem/jsTest/blob/master/promise/final/promise.dist.js)
```$xslt
class util {
  static isFunction(value) {
    return typeof value === 'function'
  }

  static isArray(value) {
    return Array.isArray(value)
  }
}

/**
 * Promise
 * @constructor
 */
class Promise extends util {
  constructor(executor) {
    /**
     * 继承绑定，作为context调用父类的constructor
     */
    super(executor)

    if (!Promise.isFunction(executor)) throw new TypeError('参数必须是一个函数')
    /**
     * @description
     * 状态分为 PENDING  可以过度到RESOLVED或REJECTED
     *        RESOLVED
     *        REJECTED
     * @type {string}
     * @private
     */
    this._status = 'PENDING'
    /**
     * 正确值
     * @type {Object}
     * @private
     */
    this._value = Object.create(null)
    /**
     * 错误值
     * @type {Object}
     * @private
     */
    this._reason = Object.create(null)
    /**
     * 储存错误回调
     * @type {Array}
     * @private
     */
    this._rejecteds = []
    /**
     * 正确回调的方法集 
     * @type {Array}
     * @private
     */
    this._deferreds = []
    /**
     * 在class中 constructor里面的函数无法直接访问this
     * 为了形成private，所以不放外面
     * @type {Promise}
     * @private
     */
    let _this = this

    /**
     * 执行承诺的函数
     * @param value
     */
    function resolve(value) {
      /**
       * 异步，为了让then先执行，注册进回调
       */
      setTimeout(() => {
        try {
          if (_this._status === 'PENDING') {
            _this._status = 'RESOLVED'
            _this._value = value
            /**
             * 循环执行所有的回调
             */
            _this._deferreds.forEach((deferred) => {
              deferred(value)
            })

          }
        } catch (e) {
          reject(e)
        }

      })
    }

    function reject(reason) {
      setTimeout(() => {
        try {
          if (_this._status === 'PENDING') {
            _this._status = 'REJECTED'
            _this._reason = reason

            _this._rejecteds.forEach((rejected) => {
              rejected(reason)
            })
          }
        } catch (e) {
          reject(e)
        }
      })
    }

    try {
      executor(resolve, reject)
    } catch (e) {
      reject(e)
    }

  }

  /**
   * resolve方法
   * @example Promise.resolve('test').then(function(result){ result // test })
   * @param value
   * @returns {Promise}
   */
  static resolve(value) {
    return new Promise((resolve, reject) => {
      resolve(value)
    })
  }

  /**
   * all方法 当需要许多promise一起执行的时候用，最后返回一个存有所有promise返回值的数组
   * 当一个promise reject掉了，认为此all方法执行失败，进入reject
   * @param promises
   * @returns {Promise}
   */
  static all(promises) {
    if (!Promise.isArray(promises)) throw new TypeError('promises 必须是一个数组')

    /**
     * 返回一个Promise
     */
    return new Promise((resolve, reject) => {
      let result = [],
        len = promises.length

      /**
       * 将所有的返回值存储起来
       * @param value
       */
      function resolveAll(value) {
        result.push(value)

        if (--len === 0) {
          resolve(result)
        }
      }

      promises.forEach(function (promise) {
        /**
         * 将成功后的加入result数组
         * 一旦有一个失败，则直接返回失败
         */
        promise.then(resolveAll, reject)
      })

    })
  }

  /**
   * 方便使用，封装catch方法
   * @param onRejected
   */
  catch(onRejected) {
    this.then(null, onRejected)
  }

  /**
   * 重点方法，then
   * @param onFulfilled
   * @param onRejected
   * @returns {Promise}
   */
  then(onFulfilled, onRejected) {
    /**
     * 每次执行then都会返回一个promise供链式调用
     */
    return new Promise((resolve, reject) => {
      let returnedValue = Object.create(null)
      /**
       * 如果是RESOLVED状态说明执行了过resolve函数，this.value是有值的
       * e.g. Promise.resolve('resolve').then(function(result){})
       * 最主要看onFulfilled返回值是什么
       */
      /**
       * _RESOLVED
       * @param value
       * @private
       */
      function _RESOLVED(value) {
        /**
         * 取得onFulfilled的返回值，判断是否返回一个promise
         * 回调方式的then
         * @example
         * new Promise(function(resolve, reject){
         *  resolve('test')
         * })
         *  .then(function(result){
         *    return new Promise(function(resolve, reject){
         *      resolve(result + 'test')
         *    })
         *      .then()
         *  })
         */
        returnedValue = Promise.isFunction(onFulfilled) && onFulfilled(value) || value

        try {
          if (returnedValue && returnedValue instanceof Promise) {
            returnedValue.then((value) => {
              resolve(value)
            }, (reason) => {
              reject(reason)
            })
          } else {
            resolve(returnedValue)
          }

        } catch (e) {
          reject(e)
        }
      }

      /**
       * 失败的错误
       * @param reason
       * @private
       */
      function _REJECTED(reason) {
        returnedValue = Promise.isFunction(onRejected) && onRejected(reason) || reason

        reject(returnedValue)
      }

      /**
       * 直接调用resolve的情况
       * @example
       * Promise.resolve('test')
       *  .then(function() {})
       */

      if (this._status === 'RESOLVED') {
        _RESOLVED(this._value)

      } else if (this._status === 'REJECTED') {
        _REJECTED(this._reason)

      } else if (this._status === 'PENDING') {
        /**
         * e.g. new Promise().then(function(result){})
         */
        this._deferreds.push(_RESOLVED)

        this._rejecteds.push(_REJECTED)
      }

    })

  }

}
/**
 * 将类返回，外面用babel编译
 * @type {Promise}
 */
module.exports = Promise
```
