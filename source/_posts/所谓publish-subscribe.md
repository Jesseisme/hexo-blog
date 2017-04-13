---
title: 所谓publish-subscribe
categories: 课外学习
tags: 
  - 设计模式
  - javascript
---
## 所谓观察者模式 publish/subscribe
1. subscribe事件订阅/事件监听 就是将事件和事件的callback，  
添加到一个需要监听执行事件队列里面
    {
      eventName: [callback1, callback1],
      eventName: [callback]
    }
2. 接收到publish发布事件就是将subscribe的事件从队列拿出来执行
3. 在外看来就像是一直在监测这个事件，然后接收到publish发布的，  
执行监测的事件callback