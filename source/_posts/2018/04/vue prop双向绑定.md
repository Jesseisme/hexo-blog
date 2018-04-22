---
title: prop双向绑定
date: 2018-04-21 09:42:31
categories: 人生很美好
tags:
  - vue
---
## Vue单项数据流
vue的组件间传值使用的是prop。按照vue的设计理念，prop的传值应该是单向的，因为这样会避免父组件将值传给多个子组件时某一个子组件数据变动导致的其他有数据依赖的组件同步更新，而消耗性能。但某些情况下，我们必须要使用双向数据流，应该怎么办呢？

<!-- more -->

### 思考
1. 引用类型的数据可以直接传递，引用类型由于传递的是一个地址，所以默认会进行双向数据流动。在传递引用数据类型的时候，考虑使用**immutable**或者**cloneDeep**去阻止数据的双向流动。
2. 基本类型，由于基本类型传递的是值，所以本次主要解决的是基本类型的双向传值。

### 解决
1.首先想到用事件去进行解决   
```html
<div id="container">
  <div v-show="showComponent">test</div>
 <test-component :is-show="showComponent" @update="(val) => showComponent = val"></test-component>
</div>
```

```typescript
const TestComponent = Vue.component('test-component', {
  template: `
  <div>
    <button @click="toogle">toogle</button>
    <div style="background: #ff0000" v-show="isShowMark">test</div>
  </div>
`,
  props: {
    isShow: {
      default: () => false
    }
  },
  data () {
    return {
      isShowMark: this.isShow
    }
  },
  watch: {
    isShowMark (val) {
      this.$emit('update', val)
    }
  },
  methods: {
    toogle () {
      this.isShowMark = !this.isShowMark
    }
  }
})

new Vue({
  el: '#container',
  data: {
    showComponent: true
  },
  methods: {
    doLogin: function() {
      this.submitData = this.user;
    }
  },
  components: {
    'test-component': TestComponent
  }
});
```
解决问题，但这样每次写一个方法是不是有点麻烦，vue有一个属性叫做```sync```，就是一个简写的语法糖.
```html
<div id="container">
  <div v-show="showComponent">test</div>
 <test-component :is-show.sync="showComponent"></test-component>
</div>
```
```typescript
const TestComponent = Vue.component('test-component', {
  template: `
  <div>
    <button @click="toogle">toogle</button>
    <div style="background: #ff0000" v-show="isShowMark">test</div>
  </div>
`,
  props: {
    isShow: {
      default: () => false
    }
  },
  data () {
    return {
      isShowMark: this.isShow
    }
  },
  watch: {
    isShowMark (val) {
      this.$emit('update:isShow', val)
    }
  },
  methods: {
    toogle () {
      this.isShowMark = !this.isShowMark
    }
  }
})

new Vue({
  el: '#container',
  data: {
    showComponent: true
  },
  methods: {
    doLogin: function() {
      this.submitData = this.user;
    }
  },
  components: {
    'test-component': TestComponent
  }
});
```
[codePen](https://codepen.io/slipkinem/pen/XqmWQP?editors=1010#code-area)