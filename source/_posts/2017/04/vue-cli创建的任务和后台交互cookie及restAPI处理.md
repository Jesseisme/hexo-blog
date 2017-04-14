---
title: vue-cli创建的任务和后台交互cookie及restAPI处理
categories: 课外学习
tags:
  - vue
  - cookie
  - node.js
  - javascript
---
#### 在写vue和JAVA后台进行交互的时候，后端总是拿不到前端的cookie，  
经检查是cookie的path作用域的原因，可以这样解决。
```
 let options = proxyTable[ctx]

  if (typeof options === 'string') {
    options = {
      target: options,
      changeOrigin: true,

      onProxyRes(proxyRes, req, res) {
      proxyRes.headers['set-cookie'] = 
        [].slice.call(proxyRes.headers['set-cookie'] || '')  
        .map(item => {
          return item.replace(/Path=\/.*?;/, 'Path=/;')
        })
      }

    }
  }
```
<!-- more -->
这段代码就是将proxyRes.headers['set-cookie']的path=/xx 转变成 path=/


### 有时候在做代理请求的时候，需要拦截处理req的设置，  
查看**http-proxy-middleware**的文档，可以用filter做拦截，代码：
```
app.use(proxyMiddleware((pathName, req) => {
    pathName = req.originalUrl = req.url = `${rootAPI}${req.url}`

    return pathName.match(ctx)
  }, options))
```

#### 最后vue proxy这块的总代码：
```
Object.keys(proxyTable).forEach(ctx => {
  let options = proxyTable[ctx]

  if (typeof options === 'string') {
    options = {
      target: options,
      changeOrigin: true,
      onProxyRes(proxyRes, req, res) {
        proxyRes.headers['set-cookie'] = 
          [].slice.call(proxyRes.headers['set-cookie'] || '')  
            .map(item => {
              return item.replace(/Path=\/.*?;/, 'Path=/;')
            })
          }
      }
    }
  }

  app.use(proxyMiddleware((pathName, req) => {
    pathName = req.originalUrl = req.url = `${rootAPI}${req.url}`    
    // 可以在此处进行处理request请求

    return pathName.match(ctx)
  }, options))

})
```
