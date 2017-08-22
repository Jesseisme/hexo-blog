---
title: js设计模式笔记-组合模式
date: 2017-08-21 18:33:04
tags:
  - 设计模式
  - js基础
---

## 组合模式
1. 层次结构，将部分整合成一个整体，类似DOM tree，所有有节点组合成一个html，使得用户对单个对象和组合的对象具有一致性
  * 有一个整体作为对外的接口，内部组合若干的层次，模块化每个组件

<!-- more -->

```
// util
function inheritObject (o) {
  function F () {
  }

  F.prototype = o
  return new F()
}

function inheritPrototype (subClass, superClass) {
  let p = inheritObject(superClass.prototype)
  p.constructor = subClass
  subClass.prototype = p
}

// 总类
function News () {
  this.children = []
  this.element = null
}

// 抽象
News.prototype = {
  constructor: 'News',
  init: function () {
    throw new Error('please override your method')
  },
  add: function () {
    throw new Error('please override your method')
  },
  getElement: function () {
    throw new Error('please override your method')
  }
}

// 容器
function Container (id, parent) {
  News.call(this)
  this.id = id
  this.parent = parent
}

inheritPrototype(Container, News)

Container.prototype.init = function () {
  this.element = document.createElement('ul')
  this.element.id = this.id
  this.element.className = 'new-container'
}

Container.prototype.add = function (child) {
  this.children.push(child)
  this.element.appendChild(child.getElement())
  return this
}

Container.prototype.getElement = function () {
  return this.element
}

Container.prototype.show = function () {
  this.parent.appendChild(this.element)
}

// 下一层级的集合类
function Item (className) {
  News.call(this)
  this.className = className
  this.init()
}

inheritPrototype(Item, News)

Item.prototype.init = function () {
  this.element = document.createElement('li')
  this.element.className = this.className
}

Item.prototype.add = function (child) {
  this.children.push(child)
  this.element.appendChild(child.getElement())
  return this
}

Item.prototype.getElement = function () {
  return this.element
}

var NewsGroup = function (className = '') {
  News.call(this)
  this.className = className
  this.init()
}

inheritPrototype(NewsGroup, News)

NewsGroup.prototype.init = function () {
  this.element = document.createElement('div')
  this.element.className = this.className
}

NewsGroup.prototype.add = function (child) {
  this.children.push(child)
  this.element.appendChild(child.getElement())
}

NewsGroup.prototype.getElement = function () {
  return this.element
}

var ImageNews = function (url = '', href = '#', className = 'normal') {
  News.call(this)
  this.url = url
  this.href = href
  this.className = className
  this.init()
}

inheritPrototype(ImageNews, News)

ImageNews.prototype.init = function () {
  this.element = document.createElement('a')
  var img = new Image()
  img.src = this.url
  img.href = this.href
  this.element.appendChild(img)
  this.element.className = 'image-news ' + this.className
  this.element.href = this.href
}

ImageNews.prototype.add = function () {
}

ImageNews.prototype.getElement = function () {
  return this.element
}

var IconNews = function (text = '', href = '#', type = 'video') {
  News.call(this)
  this.text = text
  this.href = href
  this.type = type
  this.init()
}

inheritPrototype(IconNews, News)

IconNews.prototype.init = function () {
  this.element = document.createElement('a')
  this.element.innerHTML = this.text
  this.element.href = this.href
  this.element.className = 'icon ' + this.type
}

IconNews.prototype.add = function () {
}

IconNews.prototype.getElement = function () {
  return this.element
}

var EasyNews = function (text = '', href = '#') {
  News.call(this)
  this.text = text
  this.href = href
  this.init()
}

inheritPrototype(EasyNews, News)

EasyNews.prototype.init = function () {
  this.element = document.createElement('a')
  this.element.innerHTML = this.text
  this.element.href = this.href
  this.element.className = 'text'
}

EasyNews.prototype.add = function () {
}

EasyNews.prototype.getElement = function () {
  return this.element
}

function TypeNews (text = '', href = '#', type = '', pos = 'left') {
  News.call(this)
  this.text = text
  this.href = href
  this.type = type
  this.pos = pos
  this.init()
}

inheritPrototype(TypeNews, News)

TypeNews.prototype.init = function () {
  this.element = document.createElement('a')
  if (this.pos === 'left') {
    this.element.innerHTML = '[' + this.type + '] ' + this.text
  } else {
    this.element.innerHTML = this.text + ' [' + this.type + ']'
  }

  this.element.href = this.href
  this.element.className = 'text'
}

TypeNews.prototype.add = function () {
}

TypeNews.prototype.getElement = function () {
  return this.element
}

// 层级已经声明，开始组合
var news1 = new Container('news', document.body)
news1
  .add(
    new Item('normal')
      .add(
        new IconNews('fkdsjflksdjflkjsdlkfjlskdjf', '#', 'video')
      )
  )
  .add(
    new Item('normal')
      .add(
        new IconNews('[fdsfdsfdsfsdfsdfdasf', '#', 'live')
      )
  )
  .add(
    new NewsGroup('has-img')
      .add(
        new ImageNews('img/1.jpg', '#', 'small')
      )
      .add(
        new EasyNews('dsfdsfsdfdsfsdfd', '#')
      )
      .add(
        new EasyNews('xxxxxxxxxxxxxx', '#')
      )
  )
```