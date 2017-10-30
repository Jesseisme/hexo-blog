---
title: 修改vue的keep-alive实现仿easyui-页面tab切换
date: 2017-10-27 17:08:06
tags:
  - vue
  - tabs
  - vue-router
---

## 后台管理页面通常会有tabs切换作为导航
### 常见实现方式
1. 通过显示和隐藏div（缺点：无法看到路由）
2. 通过iframe，其实和显示隐藏区别不大
### vue实现方式
因为要在vue中实现，用vue-router和vue中一个keep-alive，但是keep-alive有个缺点，他是用对象来缓存组件，并且是一个抽象组件，所以就稍微修改下。

### 效果图
![navigation](navigation.png)

<!-- more -->

### 功能
1. 点击左侧显示toolbar nav
2. 通过toolbar 切换路由，并保持之前缓存
3. 关闭toolbar清除缓存，打开后仍可用缓存

### 项目源代码
[https://github.com/slipkinem/vue-admin](https://github.com/slipkinem/vue-admin)

### 实现方式
监听路由的变动，当路由改变时将当前路由添加到一个列表里面。循环此列表生成**toolbar**的``tabs``，给keep-alive添加两个方法。第一个是当**keep-alive**工作的时候，设置存储``key``的**hook**、第二个方法添加通过``key``删除缓存``removeCacheByKey``。
当点击关闭按钮的时候，调用``removeCacheByKey``就可以完美解决vue keep-alive无法主动清除缓存的问题了。主要的keep-alive代码：
```javascript
import _ from 'lodash'

function isDef (val) {
  return val !== undefined && val !== null
}

function getFirstComponentChild (children) {
  if (Array.isArray(children)) {
    for (let i = 0; i < children.length; i++) {
      const c = children[i]
      if (isDef(c) && isDef(c.componentOptions)) {
        return c
      }
    }
  }
}

function getComponentName (opts) {
  return opts && (opts.Ctor.options.name || opts.tag)
}

function matches (pattern, name) {
  if (Array.isArray(pattern)) {
    return pattern.indexOf(name) > -1
  } else if (typeof pattern === 'string') {
    return pattern.split(',').indexOf(name) > -1
  } else if (_.isRegExp(pattern)) {
    return pattern.test(name)
  }
  /* istanbul ignore next */
  return false
}

function pruneCache (cache, current, filter) {
  for (const key in cache) {
    const cachedNode = cache[key]
    if (cachedNode) {
      const name = getComponentName(cachedNode.componentOptions)
      if (name && !filter(name)) {
        if (cachedNode !== current) {
          pruneCacheEntry(cachedNode)
        }
        cache[key] = null
      }
    }
  }
}

function pruneCacheEntry (vnode) {
  if (vnode) {
    vnode.componentInstance.$destroy()
  }
}

export default {
  name: 'pk-keep-alive',

  props: {
    include: [],
    exclude: [],
    updateComponentsKey: Function
  },

  created () {
    // vue的keep-alive存储对象
    this.cache = Object.create(null)
  },
  // 调用keep-alive组件销毁钩子，组件销毁的时候同时清除缓存
  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache[key])
    }
  },

  watch: {
    include (val) {
      pruneCache(this.cache, this._vnode, name => matches(val, name))
    },
    exclude (val) {
      pruneCache(this.cache, this._vnode, name => !matches(val, name))
    }
  },

  render () {
    const vnode = getFirstComponentChild(this.$slots.default)
    const componentOptions = vnode && vnode.componentOptions
    if (componentOptions) {
      // check pattern
      const name = getComponentName(componentOptions)
      if (name && (
          (this.include && !matches(this.include, name)) ||
          (this.exclude && matches(this.exclude, name))
        )) {
        return vnode
      }
      const key = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
        // 添加获取key的外部hook
      this.updateComponentsKey && this.updateComponentsKey(key)
      if (this.cache[key]) {
        vnode.componentInstance = this.cache[key].componentInstance
      } else {
        this.cache[key] = vnode
      }
      vnode.data.keepAlive = true
    }
    return vnode
  },
  methods: {
    // 通过cache的key删除对应的缓存
    removeCacheByKey (key) {
      pruneCacheEntry(this.cache[key])
      this.cache[key] = null
    }
  }
}
```