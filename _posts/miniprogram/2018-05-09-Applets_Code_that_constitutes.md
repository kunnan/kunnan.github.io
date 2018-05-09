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

# JSON 配置

>* JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式
>```
>json简单说就是javascript中的对象和数组，所以这两种结构就是对象和数组两种结构，通过这两种结构可以表示各种复杂的结构
>```
>
>

#### [小程序全局配置 app.json](https://developers.weixin.qq.com/miniprogram/dev/framework/config.html)

>* `app.json`文件用来对微信小程序进行全局配置，决定页面文件的路径、窗口表现、设置网络超时时间、设置多 tab 等。
><script src="https://gist.github.com/zhangkn/f88b35ced61ee664a9a9ba3cf7305e9d.js"></script>
>












# See Also 

>* [file.html](https://developers.weixin.qq.com/miniprogram/dev/quickstart/basic/file.html)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Applets_Code_that_constitutes 小程序的代码构成 -t Applets
> #原来""的参数，需要自己加上""
```

