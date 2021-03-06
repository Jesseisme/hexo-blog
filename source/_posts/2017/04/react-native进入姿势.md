---
title: react-native进入姿势
date: 2017-04-22 11:34:51
categories: 自学历程
tags:
  - javascript
  - reactNative
---
## 开始战斗
从react到react native, 已经走过许多时间。react native是一个高性能的用javascript开发app的框架。  
准备学一点react，然后觉得不如直接学习react-native，就开始了react-native之路，从环境搭建到一个小DEMO，花了不少时间，主要是下载太麻烦
### 准备工作
   1. ide: 我选择idea，习惯了
   2. 电脑：win7的i5渣配置，所以先不打算进行ios开发
   3. 没有vpn，宽带是交话费送的10M联通宽带，网速还行1m/s
   4. 推荐给电脑安装**[wox](http://www.getwox.com/)和everything(wox插件就有)**，方便寻找文件

## 环境搭建
### node环境
1. 下载安装**nodeJs**（最新的就行），为了防止环境变量有问题，不要修改安装目录
2. 使用 node-v npm -v 看看有没有安装成功
<!-- more -->
3. 注册淘宝镜像 
```$xslt
> npm config set registry https://registry.npm.taobao.org --global
> npm config set disturl https://npm.taobao.org/dist --global
```

4. 下载**yarn**，react-native默认使用yarn下载
```$xslt
  > npm i -g yarn
  > yarn -v
```

5. 给**yarn**注册淘宝镜像
```$xslt
> yarn config set registry https://registry.npm.taobao.org --global
> yarn config set disturl https://npm.taobao.org/dist --global 
```

### react-native安装
  1. 使用yarn下载react-native-cli
```bash
> yarn global add react-native-cli
> react-native --version
```

  2. 创建项目,一直等待下载完成，生成的目录还是很清晰的
```bash
> react-native init rnDemo 
```

  ![directory](directory.jpg)
  3. 试着运行项目
```git
> cd rnDemo
> npm start
```

   命令行显示，8081端口开启服务。在浏览器输入[localhost:8081](localhost:8081),显示``React Native packager is running.``，说明服务还是通畅的。可咱们要做安卓项目！所以继续使用命令行
```$xslt
> react-native run-android
```

   不出意外会挺慢，因为要下载gradle（gradle是安卓项目常用的构建工具，用来管理jar包和执行任务）和jar包，命令行会提示在此url下载文件``Download https://jcenter.bintray.com/com/android/tools/annotations/25.2.3/annotations-25.2.3.jar``
  4. 因为网络的问题，所以给gradle配置阿里云maven（java常用的包管理工具，有一个下载的仓库，但很慢）镜像。进入**.gradle**文件夹，一般在``C:\Users\Administrator\.gradle``，使用wox就简单多了
    ![wox](wox.jpg)  
  在 **.gradle** 文件夹里面新建 **init.gradle** 文件，并写入下面代码，或[点击下载init.gradle](init.gradle)
```groovy
allprojects{
    repositories {
        def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }
        maven {
            url REPOSITORY_URL
        }
    }
}
```

  5. 继续运行命令``react-native run-android``
```git
> react-native run-android
```

可以看到下载速度快了好多，但最后会发现报错``SDK location not found``，这是因为没有安装 **android sdk** ，接下来配置 **android环境**

### android开发环境配置
1. 安装java环境，搜一下有N多教程
2. 下载**android studio** 没有翻墙的话推荐使用[网盘下载](https://pan.baidu.com/s/1jIyKHjK)
3. 安装**android studio** 推荐使用默认安装一直点击``next``  
4. 第一次启动会进入下面界面
![androids](androids.jpg)
  * standard 标准配置，推荐第一次安装使用能够
  * custom 选择安装， 可以自己定制安装选项  
由于是第一此安装就直接选用默认的**standard**安装，但是react-native官网推荐custom
4. 记得勾选**Android Virtual Device**，一直**next**，最后点击**finished**等待下载完成
  ![avd](avd.jpg)
  
### 环境变量
在电脑环境变量设置里面，点击新建，ANDROID_HOME sdk路径
![ANDROID_HOME](android_home.jpg)  
然后再到环境变量 **path** 里面添加类似这样  
``C:\Users\Administrator\AppData\Local\Android\Sdk\tools``  
``C:\Users\Administrator\AppData\Local\Android\Sdk\platform-tools``  
一定注意路径的正确性  
![tools](tools.jpg)

### 再次运行
1. 运行``react-native run-android``
```git
> react-native run-android
```

又提示报错关于 ``[Android SDK Platform 23, Android SDK Build-Tools 23.0.1].``， 这是由于少了build-tools 23.0.1, 在sdk manager里面安装就行了  
![snap3555](snap3555.jpg)  
![snap3556](snap3556.jpg)  
点击**finished**

2. 运行``react-native run-android``
```git
> react-native run-android
```
提示报错`` com.android.builder.testing.api.DeviceException: No connected devices!``, 由于没连上设备导致的错误

### 连接设备
adb（连接devices的驱动）可以通过模拟器和真机进行，android studio带的模拟器不怎么好用，所以我选择用强大的**genymotion**进行开发

#### 下载genymotion免费版并启动
1. 下载并安装[genymotion免费版](https://pan.baidu.com/share/link?shareid=3943454172&uk=3073382768#list/path=%2F),替换安装目录文件就免费了
2. 由于**genymotion**依赖virtualbox，下载安装[virtualbox](https://www.baidu.com/s?ie=utf-8&f=3&rsv_bp=1&rsv_idx=1&tn=baidu&wd=virtualbox&oq=genymotion%25E7%25A0%25B4%25E8%25A7%25A3%25E7%2589%2588&rsv_pq=e656eb5a0011ec27&rsv_t=715efDZI0lJ%2B1tsmUpCuiiAat7RfExGnvVtlkSWbKVbC%2BS9zwTu7ku7rueA&rqlang=cn&rsv_enter=1&inputT=1128&rsv_sug3=8&rsv_sug1=8&rsv_sug7=100&bs=genymotion%E7%A0%B4%E8%A7%A3%E7%89%88)就行了
3. 以上安装好之后打开**genymotion**，根据提示添加一个设备并运行该安卓模拟器
![gen](gen.jpg)
4. 安装完毕，选择一个添加好的虚拟机，并启动。 糟糕，不知什么原因报错了。
![err](err.jpg)  
``碰到这种报错不用怕，打开virtualBox，直接启动安装好的虚拟机。``
![verr](verr.jpg)  
``可以看出来是virtualBox的配置有错误，那就打开配置信息 `` 
![configerr](configerr.jpg)  
``跟随黄色提示配置好virtualBox,继续启动在genymotion里面添加的设备，完美! `` 
![success](success.jpg)  
5. 在genymotion里面配置sdk路径
![sdk](sdk.png)
6. 如果还是提示连接不上设备，就用adb连接一下
```$xslt
> adb devices
> adb connect 127.0.0.1
> adb devices
```

## 开始开发

1. 各种配置都设置好了，继续运行``react-native run-android``
2. 尼玛又是error,这次明显的看出提示没有连接上js Service,得手动连接了    
![netErr](netErr.jpg)  
``点击菜单 -> Dev Setting -> Debug server host``
![dev](dev.jpg)  
![host](host.jpg)    
``然后返回，reload一下``
![reload](reload.jpg)  
``出现成功界面，可以愉快的开发了``  
![devSuccess](devSuccess.jpg)  
**注意：在菜单里面点击 Enable Hot Reloading，这样就可以保存后实时刷新了**

### 正式开发，就写一个简单的底部导航路由
用到底部导航package [react-native-tab-navigator](https://github.com/expo/react-native-tab-navigator)
```bash
> yarn add react-native-tab-navigator
```

修改``index.android.js``,给入口少搁点东西，不然看着恶心
```js
import React, { Component } from 'react';
import {
  AppRegistry
} from 'react-native';

import RootApp from './app/RootApp'

AppRegistry.registerComponent('rnDemo', () => RootApp); // rnDemo是项目的名称
```

在根目录新建文件夹app，在app里面新建 RootApp.js、components/Home.js、components/Nice.js  

app/RootApp.js
```js
import React, {Component} from 'react'
import {
  View,
  StyleSheet
} from 'react-native'
import TabNavigator from 'react-native-tab-navigator'
import Home from './components/Home'
import Nice from './components/Nice'
import TabNavigatorItem from "react-native-tab-navigator/TabNavigatorItem";

export default class RootApp extends Component {
  constructor(props) {
    super(props)
    this.state = {
      selectedTab: 'home'
    }
  }

  render() {
    return (
      <View style={styles.container}>
        <TabNavigator>
          <TabNavigatorItem
            title="Home"
            onPress={() => this.setState({selectedTab: 'home'})}
            selected={this.state.selectedTab === 'home'}>
            <Home/>
          </TabNavigatorItem>
          <TabNavigatorItem
            title="Nice"
            onPress={() => this.setState({selectedTab: 'nice'})}
            selected={this.state.selectedTab === 'nice'}>
            <Nice/>
          </TabNavigatorItem>
        </TabNavigator>
      </View>
    )
  }
}

let styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ccc'
  }
});
```

app/components/Home.js
```js
import React, {Component} from 'react'
import {
  Text
} from 'react-native'

export default class Home extends Component {
  render() {
    return (
      <Text>
        我是Home
      </Text>
    )
  }
}
```

app/components/Nice.js
```js
import React, {Component} from 'react'
import {
  Text
} from 'react-native'

export default class Nice extends Component {
  render() {
    return (
      <Text>
        我是Nice
      </Text>
    )
  }
}
```

大功告成  
![bigSuccess](bigSuccess.jpg)
