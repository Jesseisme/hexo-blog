---
title: angular权威指南笔记
categories: 课外学习
date: 2017-04-14 18:37:59
tags:
  - angular
  - javascript
---
#### angular权威指南笔记

1. $interpolate——服务插值表达式，可以设置插值表达式中的数据并``$interpolateProvider`` 可以自定义 **flag {{x}} => $x$**

2. orderBy——排序：给显示的文本排序

<!-- more -->
3. ``$parsers``  
   当用户同控制器进行交互，并且``ngModelController``  
   中的``$setViewValue()``方法被调用时，
   ``$parsers``数组中的函数会以流水线的形式被逐个调用。  
   第一个``$parse``被调用后，执行结果会传
   递给第二个``$parse``，以此类推
   
   ```
   angular.module('myApp')
     .directive('oneToTen', function() {
       return {
         require: '?ngModel',
         link: function(scope, ele, attrs, ngModel) {
             if (!ngModel) return;
             ngModel.$parsers.unshift(
             function(viewValue) {
               var i = parseInt(viewValue);
               if (i >= 0 && i < 10) {
               ngModel.$setValidity('oneToTen', true);
               return viewValue;
             } else {
               ngModel.$setValidity('oneToTen', false);
               return undefined;
           }
         });
       }
       };
     });
   ```

4. **$formatters**
  当绑定的``ngModel``值发生了变化，并经过``$parsers``  
  数组中解析器的处理后，这个值会被传递
  给``$formatters``流水线。同``$parsers``  
  数组可以修改表单的合法性状态类似， ``$formatters``中的函
  数也可以修改并格式化这些值
  ```
  angular.module('myApp')
      .directive('oneToTen', function() {
        return {
            require: '?ngModel',
            link: function(scope, ele, attrs, ngModel) {
            if (!ngModel) return;
              ngModel.$formatters.unshift(function(v) {
                return $filter('number')(v);
              });
          }
        };
      });
  ```

5. 表单验证：``ngModelController.$我是自定义``   
    取出 ``form_name.input_name.$我是自定义``
    angular可以通过``form_name.input_name.XX``   
    拿到在``directive``里面给``ngModelController``自定义的布尔值
    从而可以通过判断来进行show/hide的表单验证
  
6. 用属性声明指令比较好

7. 指令 
  **@** 绑定一次性显示的字符串 
  **=** 双向绑定会获取作用域上面的值
  **&** 绑定事件
  **yourAttr: '@myAttr'** 在``html``用``myAttr``，在指令中还是用``yourAttr``

8. ``ng-include``
  会创建一个作用域，根据作用域原型继承原理，子作用域能访问父级的值，而反过来就不行了
  所以不能在父级作用域操作``ng-include``中的数据，导致无法正常进行``$watch``
  应该给```ng-include```创建单独的controller来控制作用域
  类似的指令还有 ```ng-controller ng-app```

9. 如要避免插值字符串{&#123; &#125;}为渲染完成闪烁问题，请使用``ng-cloak``指令

10. ng-attr-(suffix) 
  如使用svg的时候报错，就是用前缀来避免
```
<svg>
  <circle cx="{{ cx }}"></circle>
  </svg> // 报错
<svg>
  <circle ng-attr-cx="{{ cx }}"><circle>
</svg>  // OK
```
11. 指令的生命周期开始于``$compile``结束于``link方法``

12. 指令API
  ```
  .directive('', ['', function(){
  
          	// Runs during compile
          	return {
  
          	  name: '',  `指令名称`
  
          	  priority: 1, `加载优先级`
  
          	  terminal: true, `是否让优先级以下的指令不运行`
  
          	  scope: &#123; &#125;, // &#123; &#125; = isolate, true = child, false/undefined = no change
          	  `作用域，如果是true则不让外部继承，外部读不到
          	    如果是对象则是隔离作用域（作用域不传递）
          	    具有隔离作用域的指令最主要的使用场景是创建可复用的组件，组件可以在未知上下文中使
                用，并且可以避免污染所处的外部作用域或不经意地污染内部作用域`
  
          	  controller: function($scope, $element, $attrs, $transclude) &#123; &#125;,
  
          	  `指令的控制`
          	  require: 'ngModel', // Array = multiple requires,   
          	                     // ? = optional（指找不到控制器传递null）,   
          	                    // ^ = check parent elements
                               // PS:可以在link方法的第四个参数指向该指令的controller
  
          	  restrict: 'A', // E = Element,   
          	                // A = Attribute,   
          	               // C = Class,  
          	              // M = Comment
          	             // 声明方式
          	             
          	  template: '', // 字符串
          	  
          	  templateUrl: '', // 指令html的地址
          	  
          	  replace: true, // 用html标签完全替代此指令
          	  
          	  transclude: true, 
          	       // 如果为true则无法正常监听数据变化，被用来，
          	      //  只有当你希望创建一个可以包含任意内容的指令时， 
          	     //  才使用transclude: true。
          	    //  配合ng-transclude插进去
          	  
          	  compile: function(tElement, tAttrs,   
                          function transclude(  
                        
                          function(scope, cloneLinkingFn){   
                        
                            return function linking(scope, elm, attrs)&#123; &#125;})),
          		
          		link: function($scope, iElm, iAttrs, controller) {
          			
          		}
          	};
          }])
  ```
13. Angular生命周期
    1. 编译阶段： 遍历整个HTML，查找处理指令，遍历指令里面所有的模板，此时还没有数据绑定，
      ng-repeat和ng-include类似的就会在在此时进行操作，减少开销
    2. compile：在数据放入指令之前安全操作DOM
    3. pre-link：将作用域与DOM连接起来之前操作dom，不安全的转换DOM
    4. post-link: 负责事件监听器，监听数据和实时的DOM操作，如果需要性能，用compile
    5. destroy
  
14. ngModel 
  * 要访问``ngModelController``必须在指令里面require
   * 不要有隔离作用域，隔离作用域会导致无法更新外部绑定的值
     * $viewValue 获取ngModel的值
     * $setViewValue 设置ngModel的值，需要手动触发``$digest``循环
     * $render自定义渲染，会在``$parser``流水线执行完后调用，慎用
     * $parsers：数组，处理加工ngModel数据，ngModel从DOM读出来的值会被当参数传入，到数据模型
     * $formatters 数组，在数据模型变的时候函数一一调用
     * $viewChangeListeners 无需``$watch``函数不需要返回值，视图中的值变化时一一调用
     * $error 没有通过验证的信息
     * $pristine 是否对控件没有交互过
     * $dirty 用户是否交互过
     * $valid 是否正确 $setValidity设置
     * $invalid 是否错误

15. constant会在所有配置config之前执行。config: 模块加载时会执行此函数

16. run 类main函数，在注入器创建之后被执行，是第一个被执行的方法，一般篇日志路由事件监听器
    每次路由改变时执行run方法
    
17. 路由
  ng-view 最高权限指令同一元素只会运行ng-view
  ng-view运行顺序
    触发$routeChangeSuccess后试图会更新
    模板会通过路由与ng-view关联
      1. 创建一个新作用域
      2. 移除上一个视图，同时清除作用域
      3. 关联当前模板
      4. 如果路由中定义的controller则与当前作用域关联起来
      5. 触发$viewContentLoaded
      6. 调用onload指定的函数（如果有）
      
  **router的resolve属性，$injector会将声明的key添加进依赖，并能注入**
  
  **$location相当于window.location的封装，但只针对视图，不能针对全局浏览器窗口**
  
18. 依赖注入
  * 对象通常过的其控制权的方式
    * 在内部创建依赖；
    * 通过全局变量进行引用；
    * 在需要的地方通过参数进行传递
  
  angular依赖注入使用$injector来管里依赖关系和实例化
  包括模块，指令，控制器的注入
  在运行时， 任何模块启动时$injector都会负责实例化，并将其需要的所有依赖传递进去
   ```
   angular.module('myApp', [])
        .factory('greeter', function() {
          return {
            greet: function(msg) {alert(msg);}
          }
        })
        .controller('MyController',
          function($scope, greeter) {
            $scope.sayHello = function() {
            greeter.greet("Hello!");
          };
        });
        
        <div ng-app="myApp">
          <div ng-controller="MyController">
            <button ng-click="sayHello()">Hello</button>
          </div>
        </div
   ```
##### angular加载过程
```
  // 使用注入器加载应用
  var injector = angular.injector(['ng', 'myApp']);
  // 通过注入器加载$controller服务： var $controller = injector.get('$controller');
  var scope = injector.get('$rootScope').$new();
  // 加载控制器并传入一个作用域，同AngularJS在运行时做的一样
  var MyController = $controller('MyController', {$scope: scope})
```
  * angular通过**annotate**函数将greeter从参数列表中提取出来
    如果没有加[]进行显式的声明，那么ng就会通过参数来获取依赖关系
  ```
    injector.annotate(function($q, myService){ })
    // annotate会返回被注入目标中的服务的名称，从而进行匹配
  ```
  * injector.invoke(func($q)) 调用方法, fn.apply(self, args)
    将参数列表转化成实例后的函数在进行调用
    大概如此：
  ```
    function Fucker() {
      this.getFucker = function () {
        console.log('motherfucker')
      }
    }
    
    function invoke(fn, self) {
      // 一般是['dep1', 'dep2', func]
      // 会找寻依赖对应的参数函数并通过方法将其实例化
      // 调用func
      fn.apply(self, [new Fucker()])
    }
    
    invoke(function (fucker) {
      fucker.getFucker() // motherfucker
    })
  ```
19. 分析ng-include为什么会导致无法正常双向绑定
  * ng-include指令会生成一个作用域 (child)
  * ng-include父节点的作用域（parent）
  * parent.flag = "test" 根据原型继承原理 child._proto_.flag = "test"
  * 当parent.flag变成"dev" 则child._proto_.flag = "dev"
  * 如果child.flag = "product" 则 child.flag 与 parent.flag 失去联系
  * 解决这种问题需要，将要传递的属性变为对象形式
```
  function Parent() {
    this.face = "哈哈"
  }
  
  Parent.prototype = {
    constructor: Parent,
  
    $new: function () {
      var child
  
      this.$$childScope = function () {
        this.$id = 112212
      }
      this.$$childScope.prototype = this
      child = new this.$$childScope()
  
      return child
    }
  
  }
  var parentScope = new Parent()
  
  var childScope = parentScope.$new()
  
console.log(
  [parentScope.face, parentScope.fixed.face],
  [childScope.face, childScope.fixed.face]
) // ["哈哈", "哈哈"] ["哈哈", "哈哈"]
  
  
parentScope.face = "test"
parentScope.fixed.face = "test"
console.log(
  [parentScope.face, parentScope.fixed.face],
  [childScope.face, childScope.fixed.face]
) // ["test", "test"] ["test", "test"]
  
  
childScope.face = "我是谁"
childScope.fixed.face = "我是谁"
console.log(
  [parentScope.face, parentScope.fixed.face],
  [childScope.face, childScope.fixed.face]
)// ["test", "我是谁"] ["我是谁", "我是谁"]
  
```
20. $scope生命周期
  * 创建
    * 创建控制器或指令时，angular会用``$injector``创建一个作用域，并在新建的控制器指令中将作用域传递进去
        * ``$scope``会通过``$new``创建通过原型与父级保持联系
  * 链接
    * 用$watch将数据监听
  * 更新
    * 当$watch监听到变化，触发回调
  * 销毁
    * 视图不需要会销毁自己
  