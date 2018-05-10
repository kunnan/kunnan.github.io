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










# See Also 

>* [file.html](https://developers.weixin.qq.com/miniprogram/dev/quickstart/basic/file.html)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Applets_Code_that_constitutes 小程序的代码构成 -t Applets
> #原来""的参数，需要自己加上""
```

