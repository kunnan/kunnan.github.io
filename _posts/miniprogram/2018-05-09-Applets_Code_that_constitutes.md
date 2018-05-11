---
layout: post
title: miniprogram_Code_that_constitutes
date: 2018-05-09
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 小程序的代码构成
---

# 前言


本文讲解： 逻辑层&基本的架构

# 框架

>* 不同类型的文件
>```
>.json 后缀的 JSON 配置文件
>.wxml 后缀的 WXML 模板文件  
>.wxss 后缀的 WXSS 样式文件
>.js 后缀的 JS 脚本逻辑文件
>```
>```
>devzkndeMacBook-Pro:iosre devzkn$ tree -L 4
.
├── app.js
├── app.json
├── app.wxss
├── pages
│   ├── index
│   │   ├── index.js
│   │   ├── index.wxml
│   │   └── index.wxss
│   └── logs
│       ├── logs.js
│       ├── logs.json
│       ├── logs.wxml
│       └── logs.wxss
├── project.config.json
└── utils
    └── util.js
>```

>* 小程序开发框架的目标是通过尽可能简单、高效的方式让开发者可以在微信中开发具有原生 APP 体验的服务。
>```
>1) 提供了视图层描述语言 WXML 和 WXSS
>2) 基于 JavaScript 的逻辑层框架
>3) 在视图层与逻辑层间提供了数据传输和事件系统，让开发者可以方便的聚焦于数据与逻辑上。
>```
>

#### 响应的数据绑定系统

>* 整个系统分为两块`视图层（View`）和`逻辑层（App Service）`
>```
>框架可以让数据与视图非常简单地保持同步。当做数据修改的时候，只需要在逻辑层修改数据，视图层就会做相应的更新。
>```
>* [例子的代码片段](wechatide://minicode/ykE7Dim36JZG)
>



#  文件结构

>* 一个小程序主体部分由三个文件组成，必须放在项目的根目录，如下：
>```
├── app.js //小程序逻辑
├── app.json //小程序公共设置
├── app.wxss //小程序公共样式表,不是必须
>```
>
>* 一个小程序页面由四个文件组成，分别是：
>```
├── pages
│   ├── index
│   │   ├── index.js
│   │   ├── index.wxml
│   │   └── index.wxss
│   └── logs // 为了方便开发者减少配置项，描述页面的四个文件必须具有相同的路径与文件名
│       ├── logs.js //页面逻辑 
│       ├── logs.json // 页面配置
│       ├── logs.wxml //页面结构
│       └── logs.wxss //页面样式表
>```


# JSON 配置

>* JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式
>```
>json简单说就是javascript中的对象和数组，所以这两种结构就是对象和数组两种结构，通过这两种结构可以表示各种复杂的结构
>```
>
>

#### [小程序全局配置 app.json](https://developers.weixin.qq.com/miniprogram/dev/framework/config.html)

描述整体程序的app

>* `app.json`文件用来对微信小程序进行全局配置，决定页面文件的路径、窗口表现、设置网络超时时间、设置多 tab 等。
><script src="https://gist.github.com/zhangkn/f88b35ced61ee664a9a9ba3cf7305e9d.js"></script>
>


#### page.json: 描述各自页面的 page

>* 每一个小程序页面也可以使用.json文件来对本页面的窗口表现进行配
置。 
>```
>页面的配置比app.json全局配置简单得多，只是设置 app.json 中的 window 配置项的内容，页面中配置项会覆盖 app.json 的 window 中相同的配置项。
>```
><script src="https://gist.github.com/zhangkn/1b1510c1531a7c91aecc8db428999aa6.js"></script>
>
>

#### [项目配置文件](https://developers.weixin.qq.com/miniprogram/dev/devtools/edit.html#%E9%A1%B9%E7%9B%AE%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)

>* 小程序开发者工具在每个项目的根目录都会生成一个 project.config.json，你在工具上做的任何配置都会写入到这个文件
>```
>当你重新安装工具或者换电脑工作时，你只要载入同一个项目的代码包，开发者工具就自动会帮你恢复到当时你开发项目时的个性化配置，其中会包括编辑器的颜色、代码上传时自动压缩等等一系列选项。
>```
>
>* 可以在项目根目录使用 project.config.json 文件对项目进行配置。
><script src="https://gist.github.com/zhangkn/b43f9947dd9098df709b69ec57690ce1.js"></script>
>* 几个默认的预处理
>```
>1) ES6 转 ES5（可以应用于编译、预览、上传），使用 "babel-core": "^6.26.0"
>2)上传代码时样式自动补全，使用 "postcss": "^6.0.1"
>3)上传代码时自动压缩，使用 "uglify-js": "3.0.27"
>```




# 逻辑层(App Service)


>* 小程序开发框架的逻辑层由 JavaScript 编写。
>```
>逻辑层将数据进行处理后发送给视图层，同时接受视图层的事件反馈。
>```
>
>* 在 JavaScript 基础上的一些修改
><script src="https://gist.github.com/zhangkn/ee780a5f666c98a7af20ebc471b16e56.js"></script>

#### 注册小程序 App

>* App() 函数用来注册一个小程序。接受一个 object 参数，其指定小程序的生命周期函数等。
><script src="https://gist.github.com/zhangkn/e199024423db6e11619912e80c839cb5.js"></script>
>
>
 
>* 全局的 `getApp() `函数可以用来获取到小程序实例
><script src="https://gist.github.com/zhangkn/e8ea423d299f8bd7180e360ae9132f56.js"></script>
>


#### 场景值



<script src="https://gist.github.com/zhangkn/b6cbb93c0bd0f7932c9995ae7d163ccf.js"></script>


#### page 注册页面

>* Page() 函数用来注册一个页面。
>```
>1)接受一个 object 参数，其指定页面的初始数据、生命周期函数、事件处理函数等。
>2)object 内容在页面加载时会进行一次深拷贝，需考虑数据大小对页面加载的开销
>```
><script src="https://gist.github.com/zhangkn/554c73de4823971e5205eb36f8d2926c.js"></script>
>

>* Page.prototype.route、Page.prototype.setData()
><script src="https://gist.github.com/zhangkn/a8ec5cf892cc49c6c003fd35ed0f771d.js"></script>
>
>

>* setData() 参数格式
>* [代码片段](wechatide://minicode/KlZ4iimy6IZJ)
>

####  Page 实例的生命周期


![](https://ws2.sinaimg.cn/large/006tKfTcgy1fr6b1g0nt6j30ie0s6q3k.jpg)


#### 页面路由


在小程序中所有页面的路由全部由框架进行管理。


>* 页面栈
>```
>框架以栈的形式维护了当前的所有页面。 当发生路由切换的时候，页面栈的表现如下：
>```
><script src="https://gist.github.com/zhangkn/38b02fb583b776e1432edae205796c88.js"></script>
>* getCurrentPages()
>```
>getCurrentPages() 函数用于获取当前页面栈的实例，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。
>```
>
>* 路由方式:对于路由的触发方式以及页面生命周期函数
><script src="https://gist.github.com/zhangkn/fdb97182cff5442a2c638c504e7fd4c5.js"></script>
>
>

#### 模块化


###### 文本作用域

在 JavaScript 文件中声明的变量和函数只在该文件中有效；不同的文件中可以声明相同名字的变量和函数，不会互相影响。


>* <script src="https://gist.github.com/zhangkn/090fe9c18ba07608c5649f4f86b9407b.js"></script>
>

###### 模块化

>* 可以将一些公共的代码抽离成为一个单独的` js` 文件，作为一个模块。
>```
>模块只有通过 `module.exports `或者 `exports` 才能对外暴露接口。
>```
>





# 开放能力

#### open-data

>* 用于展示微信开放的数据。
>```
>1、使用 button 组件，并将 open-type 指定为 getUserInfo 类型，获取用户基本信息。 https://developers.weixin.qq.com/miniprogram/dev/component/button.html
>2、使用 open-data 展示用户基本信息。
>```

>* [在开发者工具中预览效果](wechatide://minicode/vbdmRcmV67YB)
>




# See Also 

>* [小程序与小游戏获取用户信息接口调整，请开发者注意升级](https://developers.weixin.qq.com/blogdetail?action=get_post_info&lang=zh_CN&token=1650183953&docid=0000a26e1aca6012e896a517556c01)
>```
>为优化用户体验，使用 wx.getUserInfo 接口直接弹出授权框的开发方式将逐步不再支持
>```
><script src="https://gist.github.com/zhangkn/d77f33e8c4d8747f66b358e16465d401.js"></script>
>

>* [file.html](https://developers.weixin.qq.com/miniprogram/dev/quickstart/basic/file.html)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Applets_Code_that_constitutes 小程序的代码构成 -t Applets
> #原来""的参数，需要自己加上""
```

