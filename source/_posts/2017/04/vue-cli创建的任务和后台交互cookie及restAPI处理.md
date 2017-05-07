---
title: vue-cli创建的任务和后台交互cookie及restAPI处理
categories: 课外学习
date: 2017-04-14 11:34:51
tags:
  - vue
  - cookie
  - node.js
  - javascript
---
# 在写vue和JAVA后台进行交互的时候，后端总是拿不到前端的cookie

## session简述
### 什么是session？
  * session是服务器储存信息的一种方式，一般称之为会话，  
    以java为例``request.getSession().setAttribute(key, value)``在创建session实例时会生成一个唯一的ID，浏览器发送请求时，  
    会将set-cookie返回给浏览器，浏览器自动存储在cookie中，  
    在tomcat中，此Cookie叫做JSESSIONID
  * session关键在于sessionID所以不用cookie存也是可以的，比如可以放在request param里面，只要需要时前台发过来就行
<!-- more -->
### 什么是cookie？
  * cookie是客户端（浏览器端）存储信息的一种方式，服务器可以设置浏览器set-cookie标头  
    浏览器收到标头与数值，会以文件的形式存在与计算机中。浏览器发送请求时会自动将cookie发给服务端 
     
#### cookie的属性 domain和path
  * domain：domain表示的是cookie所在的域，默认为请求的地址，如网址为www.jb51.net/test/test.aspx，  
    那么domain默认为www.jb51.net。而跨域访问，如域A为t1.test.com，域B为t2.test.com，  
    那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为.test.com；  
    如果要在域A生产一个令域A不能访问而域B能访问的cookie就要将该cookie的domain设置为t2.test.com。
    
  * path: path表示cookie所在的目录，如baidu.com，cookie默认为根目录'/'。baidu.com/wenku/，则path=/wenku/；  
    如果在biadu.com/wenku/下面设置set-cookie，则在baidu.com下面浏览器会因为path不对应，导致在baidu.com下set-cookie失败  
  ![chrome下cookie](chrome下cookie.png)  

### session的流程
  1. request.getSession().setAttribute("username", "listen")
  2. 在http response header里面设置set-cookie返回头 set-cookie: JSESSIONID=xxxxxxxx;path=/;
  3. 浏览器看到set-cookie信息，并且设置的domain和path与浏览器当前一致，否则等待匹配的path和domain，存储以后会自动清除掉set-cookie
  4. 浏览器向服务端发送请求，将存储的cookie以key=value的形式放到请求头里面
  5. 服务端获取session request.getSession().getAttribute("username")
      * 从request请求头里面获取JSESSIONID，并找到与之对称的session实例，从此实例里面获取username
      * 一个请求session对应一个实例，sessionId是区分实例的关键

### 回到问题
  * 现在代理服务器的URI是 localhost:8080, api服务器URI是localhost:8084/articlepr/
  * api服务器设置session时，set-cookie是这样set-cookie:JSESSIONID=xxxx;path=/articlepr/;
  * 现set-cookie的domain和浏览器URI（代理服务器）都为localhost，而path不一样，所以会导致set-cookie浏览器存储cookie失败，后台也就拿不到JSESSIONID了

### 解决问题
既然是path不一样就修改path
  1. 方案 后台修改path，还得让后台搞，麻烦
  2. 方案 给代理服务器加上path后缀，使其和api服务器一样，但是这样对proxyTable有一点副作用
```$xslt
module.exports = app.listen(port, function (err) {
  if (err) {
    console.log(err)
    return
  }
  var uri = `http://localhost:${port}/articlepr/`
  console.log('Listening at ' + uri + '\n')

  // when env is testing, don't need open it
  if (process.env.NODE_ENV !== 'testing') {
    opn(uri)
  }
})
```
  3. 方案 代理服务器重写path, 现使用的方式，不麻烦后台，不修改其他东西
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
