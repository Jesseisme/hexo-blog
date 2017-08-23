---
title: js设计模式笔记-职责链模式
date: 2017-08-23 17:45:13
tags:
  - 设计模式
  - js基础
---

## 职责链模式
1. 使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。
  * 你去政府部门求人办事过吗？有时候你会遇到过官员踢球推责，你的问题在我这里能解决就解决，
     不能解决就推卸给另外个一个部门（对象）。
     至于到底谁来解决这个问题呢？政府部门就是为了可以避免屁民的请求与官员之间耦合在一起，
     让多个（部门）对象都有可能接收请求，将这些（部门）对象连接成一条链，并且沿着这条链传递请求，直到有（部门）对象处理它为止。

<!-- more -->

```javascript
/**
 * 发送请求
 * @param data
 * @param dealType
 * @param dom
 */
var sendData = function (data, dealType, dom) {
  var xhr = new XMLHttpRequest(),
    url = 'getData.php?mod=userInfo'
  xhr.onload = function (event) {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
      dealData(xhr.responseText, dealType, dom)
    } else {
      // 失败
    }
  }

  for (var i in data) {
    url += '&' + i + '=' + data[i]
  }

  xhr.open('get', url, true)
  xhr.send(null)

}

/**
 * 处理响应数据
 * @param data
 * @param dealType
 * @param dom
 */
var dealData = function (data, dealType, dom) {
  var dataType = Object.prototype.toString.call(data)
  switch (dealType) {
    case 'sug':
      if (dataType === '[object Array]') {
        return createSug(data, dom)
      }

      if (dataType === '[object Object]') {
        var newData = []
        for (var i in data) {
          newData.push(data[i])
          return createSug(newData, dom)
        }
      }
      return createSug([data], dom)
      break
    case 'validate':
      return createValidateResult(data, dom)
      break
  }
}

/**
 * 提示框组件
 * @param data
 * @param dom
 */
var createSug = function (data, dom) {
  var len = data.length,
    html = ''
  for (var i = 0; i < len; i++) {
    html += '<li>' + data[i] + '</li>'
  }

  dom.parentNode.getElementsByTagName('ul')[0].innerHTML = html
}

var createValidateResult = function (data, dom) {
  dom.parentNode.getElementsByTagName('span')[0].innerHTML = data
}
```