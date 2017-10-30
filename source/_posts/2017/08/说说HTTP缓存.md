---
title: 说说HTTP缓存
date: 2017-08-18 09:38:17
tags:
  - http
  - 缓存
---

## 浏览器缓存
### 什么是浏览器缓存 
用户访问页面的时候，对于某些资源，会将其保存在客户端。在下一次访问的时候，会将缓存从客户端拿出来，减少HTTP请求，提高用户体验。

### 浏览器缓存是什么样子的？

访问github.com，看下资源文件的报文
<!-- more -->

![github图片](github.png)  
可以看到报文里面有cache-control，Expires,Last-Modified这样的字段，这种文件就是从浏览器缓存中拿出来的，可以看出速度特别快  
![github请求图片](githubRequest.png)

### 是什么控制着浏览器对文件的缓存与否？
#### Cache-Control:
1. **max-age** 设置缓存时间，单位为秒，这个时间是指缓存的时长，即在这个时间内的这个资源都会使用这个版本，服务器文件变化了浏览器也不会改变。
2. **public** 如果没有指定，则缺省值是public，指定是浏览器或者任何web的代理中间trunk都可以进行文件的缓存，比如CDN也能缓存
3. **private** 只有用户浏览器可以缓存，这样CDN中继就缓存不到了
4. **no-cache** 表明必须要和服务器进行确认资源是否更改，如果更改了，就会返回最新的，在浏览器NetWork界面激活Disable cache，或者使用ctrl+f5强制刷新，浏览器都会给请求加上**Cache-Control:no-cache;Pragma:no-cache**
5. **no-store** 绝对性的禁止掉缓存，比no-cache强势一百倍，直接从服务器拉取资源
6. 其他的都不太常用，就不赘述了

#### Expires
**Expires**是有效期的意思，顾名思义就是设置资源的有效时间，（``Expires:Sat, 26 Aug 2017 10:24:11 GMT``即只保存到2017-08-26 10:24:11,过时过期），和**max-age**有点类似，但是没有**Cache-Control**的优先级高，同时出现**Cache-Control:max-age**会覆盖掉**Expires**。Expires需要和last-modified结合使用

#### Last-modified
&nbsp;&nbsp;&nbsp;&nbsp;服务器资源的最后修改时间，需要cache-control共用。浏览器第一请求时，会返回``Last-Modified:Wed, 21 Jun 2017 10:03:54 GMT``，指服务端最后一次修改文件的时间。浏览器读取后存到这个信息，在下次请求时会给请求头加上``If-Modified-Since:Wed, 21 Jun 2017 10:03:54 GMT``，用这个值去和服务端对比，没有修改就返回304，如果修改过就返回最新资源。

#### ETag
&nbsp;&nbsp;&nbsp;&nbsp;由服务端根据内容生成的一段hash，浏览器会拿这段hash和服务端进行验证资源是否修改（``ETag:"7c9570c4fd0d21:0"``）。请求时，浏览器会加上这段ETag，不过请求的字段叫做``If-None-Match:W/"7c9570c4fd0d21:0"``这就是ETag  
使用ETag可以解决Last-modified存在的一些问题：
   1. 某些服务器不能精确得到资源的最后修改时间，这样就无法通过最后修改时间判断资源是否更新 
   2. 如果资源修改非常频繁，在秒以下的时间内进行修改，而Last-modified只能精确到秒 
   3. 一些资源的最后修改时间改变了，但是内容没改变，使用ETag就认为资源还是没有修改的。

### 同样是200 from disk cache 和 from memory cache 区别
![disk](disk.png)  
可以看到**from memory cache**是不需要时间的0ms，而memory需要时间。  
哈哈很简单，就是一个存在disk（磁盘中），一个存在memory(内存)中，存在磁盘需要读取时间。  
存在磁盘中，退出浏览器资源还会存在，存在memory中关闭浏览器进程就会清除缓存。参考链接[memoryCache和diskCache流程详解](http://blog.csdn.net/m632587166/article/details/50732205?locationNum=14)

### 都特么几乎是后台控制的缓存，那前端呢？
1. html页面配置no-cache,html ``<meta>``标签中有个叫做http-equiv的属性，就是设置http头信息，关键字等等
    1. ``<meta http-equiv="cache-control" content="no-cache">``设置no-cache
    2. ``<meta http-equiv="expires" content="0">`` 设置Expires为0
    3. ...
2. 对于js或者css文我们一般会选择进行缓存，如果要更新的话，只需要将引入的js文件写上版本号
```html
<script src="xxx?v=0.2"></script>
```
最好的方式是给文件加上hash或md5值，对于改变了的文件更改hash，这样就能更新网站了。webpack,gulp等工具都能实现。thx

## 博客中比较流行的缓存流程图
![liucheng.png](liucheng.png)  

**cache-control**  
![cache-control.png](cache-control.png)
