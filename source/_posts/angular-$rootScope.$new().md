---
title: angular-$rootScope.$new()
---
## Scope实例上面有这么一个属性：$new()
### $new(isolate, parent)
``所有scope都是通过$new创建``
#### 参数解释

1. 参数一（isolate）设置是否为孤立作用域

- 如果是`true`，则建立孤立作用域，孤立作用域直接和祖级作用域联系 `child = new Scope(); child.$root = this.$root`
- 如果是`false`，则判断此`$rootScope`有没有子作用域，如果没有则建立一个子作用域构造函数，并且将这个子作用域的原型指向`this（`$rootScope => Scope的实例`）`,然后将`child = new this.$$ChildScope() child.__proto__ = Scope.prototype`
2. 参数二设置要`new`的作用域的`parent`，如果不设置则将`parent = this`， 也就是将`new`的`child`挂载到`this(此$rootScope)上面，然后给`parent`的子作用域排序，有个判断如果设置了`isolate`并且`parent != this`,**则作用域不监听$destory事件**

3. ## e.g
`var scope = $rootScope.$new()`
##### 没有指定参数，则1.将scope的父级指向$rootScope, 2.将scope创建为$rootScope的子作用域，并在原型上继承$rootScope的所有属性方法，返回。
`
