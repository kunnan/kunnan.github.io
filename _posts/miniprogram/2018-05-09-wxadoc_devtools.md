---
layout: post
title: wxadoc_devtools
date: 2018-05-09
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 微信开发者工具
---

# 前言

>* 全新的 微信开发者工具，集成了公众号网页调试和小程序调试两种开发模式。
>```
>1) 使用公众号网页调试，开发者可以调试微信网页授权和微信JS-SDK 详情---- 地址栏输入 URL，即可调试该网页的微信授权以及微信 JS-SDK 功能。
>2) 使用小程序调试，开发者可以完成小程序的 API 和页面的开发调试、代码查看和编辑、小程序预览和发布等功能。------项目管理页，可以新建、删除本地的项目，或者选择进入已存在的本地项目。还可以创建代码块
>```
>* 新增了两个开发辅助功能
>```
>1) 使用腾讯云，快速搭建小程序后台运行环境； 详情
>2) 申请测试报告，了解小程序在真实的移动设备上运行性能和运行效果。
>因为申请测试会占用测试机器资源，所以一个 APPID 一天只能申请一次测试报告。
>```


# 项目设置

微信小程序运行在三端：iOS、Android 和 用于调试的开发者工具。

#### 三端的脚本执行环境以及用于渲染非原生组件的环境是各不相同的：

>* 在 iOS 上，小程序的 `javascript` 代码是运行在 `JavaScriptCore` 中，是由` WKWebView` 来渲染的，环境有 iOS8、iOS9、iOS10
>* 在 Android 上，小程序的` javascript `代码是通过 `X5 JSCore`来解析，是由 `X5` 基于 `Mobile Chrome 53/57` 内核来渲染的
>* 在 开发工具上， 小程序的 `javascript `代码是运行在` nwjs `中，是由` Chrome Webview `来渲染的
>

>* 区别：
>```
>1)ES6 语法支持不一致。
>2) wxss 渲染表现不一致。尽管可以通过开启样式补全来规避大部分的问题 ，还是建议开发者需要在 iOS 和 Android 上分别检查小程序的真实表现。
>```

# Mac OS 快捷键	

#### 编辑	

>* ⇧ + ⌘ + N 	新建项目
>* ⌘ + [  	代码左缩进
>* ⇧ + ⌥ + F  	格式化代码
>* ⌥ + ⬆ 代码上移一行
>* ⇧ + ⌘ + F 项目内搜索
>

#### 工具	

>* ⇧ + ⌘ + P 预览代码

>* ⇧ + ⌘ + U	 上传代码



#  代码编辑

#### 文件支持

>* 工具目前提供了 5 种文件的编辑：
>```
>wxml、wxss、js、json、wxs 以及图片文件的预览。
>```

#### 文件操作

>* 新建页面有两种方式
>```
>1) 在目录树上右键，选择新建 Page，将自动生成页面所需要的 wxml、wxss、js、json
>2)在 app.json 的 pages 字段，添加需要新建的页面的路径，将会自动生成改页面所需要的文件----我比较喜欢这种方式
>```
>

# 调试工具




#### Wxml

>* 帮助开发者开发 wxml 转化后的界面
>```
>1) 可以看到真实的页面结构以及结构对应的 wxss 属性，同时可以通过修改对应 wxss 属性，在模拟器中实时看到修改的情况（仅为实时预览，无法保存到文件）。
>2) 通过调试模块左上角的选择器，还可以快速定位页面中组件对应的 wxml 代码。
>```
>

#### Sources panel


>* 显示当前项目的脚本文
>```
>微信小程序框架会对脚本文件进行编译的工作，所以在 Sources panel 中开发者看到的文件是经过处理之后的脚本文件，开发者的代码都会被包裹在 define 函数中，并且对于 Page 代码，在尾部会有 require 的主动调用。
>```
><script src="https://gist.github.com/zhangkn/1c58ad68605aa83c90e2ab4fa5e7416a.js"></script>
>


####  AppData panel
>*  用于显示当前项目运行时小程序 AppData 具体数据
>```
>实时地反映项目数据情况，可以在此处编辑数据，并及时地反馈到界面上。
>```
>


#### Storage panel 

>* 用于显示当前项目使用 wx.setStorage 或者 wx.setStorageSync 后的数据存储情况。
>```
>可以直接在 Storage panel 上对数据进行删除（按 delete 键）、新增、修改
>```


#### Network Panel 

>* 用于观察和显示 request 和 socket 的请求情况
>```
>注：uploadFile 和 downloadFile 暂时不支持在 Network Panel 中查看
>```
>

#### Console panel
>* Console panel 有两大功能：
>```
>1) 开发者可以在此输入和调试代码: help
>2) 小程序的错误输出，会显示在此处
```
><script src="https://gist.github.com/zhangkn/c7e66ebe5f10ddfacdc0f210f4d8d701.js"></script>
>* openVendor : 
```
openVendor
()=>q('openVendor')
devzkndeMacBook-Pro:WeappVendor devzkn$ tree -L 2
.
├── 1.0.0
│   ├── WAService.js
│   └── WAWebview.js
├── 1.9.98
│   ├── WAService.js
│   └── WAWebview.js
```

#### Sensor panel 

>* 有两大功能：
>```
>1)开发者可以在这里选择模拟地理位置
>2)开发可以在这里模拟移动设备表现，用于调试重力感应 API
>```
>


#### 自定义数据上报

>* 灵活多维和近实时的用户行为分析，可以通过自定义上报，对用户在小程序内的行为做精细化跟踪，满足页面访问等标准统计以外的个性化分析需求
>* 小程序中使用 wx.reportAnalytics API 进行的数据上报
>

>* 登录https://mp.weixin.qq.com ，进入“数据分析” – “自定义分析” - “事件管理”，点击 “新建事件”。
>```
>确认字段信息后，点击“保存并测试”，保存当前配置并测试上报的数据是否符合预期。
>```


#### 特殊场景调试


>* 带 shareTicket 的转发
>```
>1) 调用 wx.showShareMenu 的参数 withShareTicket 为 true 时，点击模拟器右上角菜单后出现的转发按钮，会出现一个测试群列表
>2) 开发者点击选取任何一个群，可以通过接口的回包获取到 shareTicket ，通过调用 wx.getShareInfo 可以获取到相关转发的信息
>```

# 真机远程调试流程


# 命令行调用

可以用来自动化的工作

>* 开发者工具提供了命令行与 HTTP 服务两种接口供外部调用
>```
>开发者可以通过命令行或 HTTP 请求指示工具进行登录、预览、上传等操作
>```

#### 命令行

>* macOS: <安装路径>/Contents/Resources/app.nw/bin/cli
>```
>/Applications/wechatwebdevtools.app/Contents/Resources/app.nw/bin
devzkndeMacBook-Pro:bin devzkn$ tree -L 2
.
└── cli
>```
>* Usage: index [options]
>```
>devzkndeMacBook-Pro:bin devzkn$ cli --help
  Usage: index [options]
  Options:
    -V, --version                        output the version number
    -o, --open [path]                    Open IDE. If path is specified, IDE will try to open the project in the path.
    -l, --login                          Login
    --login-qr-output [format@path]      Customize output of QR Code. format can be terminal or base64. If path is used, QR code will be written to the path
    -p, --preview <project_root>         Preview mini program
    --preview-qr-output [format@path]    Customize output of QR Code. format can be terminal or base64. If path is used, QR code will be written to the path
    -u, --upload <version@project_root>  Upload mini program
    --upload-desc <desc>                 Description of the uploaded version
    -t, --test <project_root>            Request an auto mobile test
    -h, --help                           output usage information
>```


######  1. 命令行启动工具

>* 建立软连接, 不行；考虑使用别名
>```
>devzkndeMacBook-Pro:bin devzkn$ ln -s /Applications/wechatwebdevtools.app/Contents/Resources/app.nw/bin/cli cli
> rm -rf cli
> alias cli='/Applications/wechatwebdevtools.app/Contents/Resources/app.nw/bin/cli'
>```

>* cli -o
>```
># 打开工具
cli -o
# 打开路径 /Users/username/demo 下的项目
cli -o base64@/Users/username/demo
>```


###### 2、登录 cli -l

<script src="https://gist.github.com/zhangkn/16292484dba54a20170c307b2b00c92c.js"></script>

###### 3. 命令行提交预览

<script src="https://gist.github.com/zhangkn/2880441c6d5546d15d4d8e7ef18cd878.js"></script>


###### 4. 命令行上传代码


<script src="https://gist.github.com/zhangkn/3f40c1e5669f9f6a80c941db5905f14e.js"></script>


###### 5. 支持自动化测试

<script src="https://gist.github.com/zhangkn/019ca9e8e6c5f1b6bdfb214c39c8961d.js"></script>


#### HTTP


http 服务在工具启动后自动开启，HTTP 服务端口号在用户目录下记录，可通过检查用户目录、检查用户目录下是否有端口文件及尝试连接来判断工具是否安装/启动。

>* 端口号文件位置：
>```
>idePortFile: /Users/devzkn/Library/Application Support/微信web开发者工具/Default/.ide
>```


##### 1. 打开工具或指定项目

<script src="https://gist.github.com/zhangkn/9d99a812e56f2669bd0a170659437f70.js"></script>

###### 2. 登录

<script src="https://gist.github.com/zhangkn/2e6000a866ea3e2b40bc2f9d8d282337.js"></script>


###### 3. 提交预览

<script src="https://gist.github.com/zhangkn/091aaf96e25eaf3b19ba22b2c52697ac.js"></script>


###### 4. 上传

<script src="https://gist.github.com/zhangkn/49fd1ecfbf66f8277f7ae249c388e39e.js"></script>

###### 5. 自动化测试

>* test
>```sh
># 提交路径为 /Users/username/demo 的项目进行测试
http://127.0.0.1:端口号/test?projectpath=%2FUsers%2Fusername%2Fdemo
>```
>


# [代码片段](https://kunnan.github.io/2018/05/09/minicode/)




# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost wxadoc_devtools 微信开发者工具 -t miniprogram
> #原来""的参数，需要自己加上""
```

