---
layout: post
title: miniprogram_framework_view
date: 2018-05-11
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 视图层
---


# 前言


>* 框架的视图层由 WXML 与 WXSS 编写，由组件（例如open-data：用于展示微信开放的数据）来进行展示。
>```
>将逻辑层的数据反应成视图，同时将视图层的事件发送给逻辑层。
>```

>* WXML(WeiXin Markup language) 用于描述页面的结构。
>* WXS(WeiXin Script) 是小程序的一套脚本语言，结合 WXML，可以构建出页面的结构。
>* WXSS(WeiXin Style Sheet) 用于描述页面的样式。
>* 组件(Component)是视图的基本组成单元。


# WXML

>* WXML（WeiXin Markup Language）是框架设计的一套标签语言，结合[`基础组件`](https://developers.weixin.qq.com/miniprogram/dev/component/)、[`事件系统`](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html)，可以构建出页面的结构。




#### 数据绑定

>* 数据绑定使用 Mustache 语法（双大括号）将变量包起来，可以作用于：`内容`、`组件属性(需要在双引号之内)
`、`控制属性(需要在双引号之内)
`、`关键字(需要在双引号之内)
`
```
<view id="item-{{id}}"> </view>
<view wx:if="{{condition}}"> </view>
<checkbox checked="{{false}}"> </checkbox>
```



###### 运算

>* 支持的有如下几种方式: `三元运算
`、`算数运算`、`逻辑判断`、`字符串运算
`、`数据路径运算`、


>* 三元运算
>```wxml
><view hidden="{{flag ? true : false}}"> Hidden </view>
>```

>* 算数运算
>```wxml
><view> {{a + b}} + {{c}} + d </view>
>```
>* 逻辑判断
>```
><view wx:if="{{length > 5}}"> </view>
>```
>* 字符串运算
>```
><view>{{"hello" + name}}</view>
>```
>* 数据路径运算
>```
><view>{{object.key}} {{array[0]}}</view>
>```
>

###### 组合


直接进行组合，构成新的对象或者数组



>* 数组
>```
><view wx:for="{{[zero, 1, 2, 3, 4]}}"> {{item}} </view>
>```
>* 对象
>```
><template is="objectCombine" data="{{for: a, bar: b}}"></template>
>```
>
>* 用扩展运算符 ... 来将一个对象展开,常用于模板的数据传入
>```
><template is="objectCombine" data="{{...obj1, ...obj2, e: 5}}"></template>
><template is="staffName" data="{{...staffA}}"></template>
>```
>
>






###### 例子

>* <!--wxml-->
>```wxml
>    <text class="user-motto">{{motto}}</text>
>```
>* //index.js
>```js
>Page({
  data: {
    text: 'Click me get my posts!',
    motto: helloData,
    }
 })
>```


#### 列表渲染

>* 在`组件`上使用 `wx:for` 控制属性绑定一个数组，即可使用数组中各项的数据重复渲染该`组件`。

>* `wx:key`  指定列表中项目的唯一的标识符
>```
>2）当数据改变触发渲染层重新渲染的时候，会校正带有 key 的组件，框架会确保他们被重新排序，而不是重新创建，以确保使组件保持自身的状态，并且提高列表渲染时的效率。
>1）使用场景：（如 <input/> 中的输入内容，<switch/> 的选中状态）
>VM339:2 ./pages/logs/logs.wxml
(anonymous) @ VM339:2
VM339:3  Now you can provide attr "wx:key" for a "wx:for" to improve performance.
  25 | 
  26 | <view class="container log-list">
> 27 |   <block wx:for="{{logs}}" wx:for-item="log" wx:for-index="knindex">
     |    ^
  28 |     <text class="log-item">{{knindex + 1}}. {{log}}</text>
  29 |   </block>
  30 | </view>
(anonymous) @ VM339:3
>```
>



###### 例子1



>* logs.wxml: 重复渲染该`text组件`
>```
><!--logs.wxml 页面结构-->
<view class="container log-list">
<!--使用 wx:for-item 可以指定数组当前元素的变量名,默认值为item -->
  <block wx:for="{{logs}}" wx:for-item="log">
  <!--使用 wx:for-index 可以指定数组当前下标的变量名：默认index-->
    <text class="log-item">{{index + 1}}. {{log}}</text>
  </block>
</view>
>```

>*  logs.js 
>```js
>//logs.js 页面逻辑
const util = require('../../utils/util.js')
Page({//进行页面的注册
  data: {
    logs: []//数组
  },
  onLoad: function () {
    util.sayHello('kunna')
    util.sayGoodbye('kunnan')
    this.setData({
      logs: (wx.getStorageSync('logs') || []).map(log => {
        return util.formatTime(new Date(log))
      })
    })
  }
})
>```
>
>* wxss
>```
>/*页面样式表*/
.log-list {
  display: flex;
  flex-direction: column;
  padding: 40rpx;
}
.log-item {
  margin: 10rpx;
}
>```


###### 例子2

>* wxml: 重复渲染该`view组件`
>```
><!--wxml-->
<view wx:for="{{array}}"> {{item}} </view>
>```
>* page.js
>```
>// page.js
Page({
  data: {
    array: [1, 2, 3, 4, 5]
  }
})
>```
>
>

#### 条件渲染


###### `wx:if` vs ` hidden`

>* ` wx:if` 也是惰性的，如果在初始渲染条件为 false，框架什么也不做，在条件第一次变成真的时候才开始局部渲染。
>
>* ` hidden` : 组件始终会被渲染，只是简单的控制显示与隐藏。
>
>
>*一般来说，`wx:if` 有更高的`切换消耗`而 `hidden `有更高的`初始渲染消耗`。
>```
>因此，如果需要频繁切换的情景下，用 hidden 更好，---频繁修改判断条件
>如果在运行时条件不大可能改变则 wx:if 较好。--- 判断条件固定
>```


###### 例子一


>* wxml
>```
>  <view class="userinfo">
  <!--1、使用 button 组件，并将 open-type 指定为 getUserInfo 类型，获取用户基本信息  -->
  <!-- /2、使用 open-data 展示用户基本信息。 -->
    <button wx:if="{{!hasUserInfo && canIUse}}" open-type="getUserInfo" bindgetuserinfo="getUserInfo"> 获取头像昵称 </button>
    <block wx:else>
      <image bindtap="bindViewTap" class="userinfo-avatar" src="{{userInfo.avatarUrl}}" background-size="cover"></image>
      <text class="userinfo-nickname">{{userInfo.nickName}}</text>
    </block>
  </view>
>```

>* js
>```js
>Page({
  data: {
    text: 'Click me get my posts!',
    motto: helloData,
    userInfo: {},
    hasUserInfo: false,
    canIUse: wx.canIUse('button.open-type.getUserInfo')//使用 button 组件，并将 open-type 指定为 getUserInfo 类型，获取用户基本信息
  }
  })
>```


###### 例子二



>* wxml
>```
><!--wxml-->
<view wx:if="{{view == 'WEBVIEW'}}"> WEBVIEW </view>
<view wx:elif="{{view == 'APP'}}"> APP </view>
<view wx:else="{{view == 'MINA'}}"> MINA </view>
>```

>*  view 变量的定义
>```
>// page.js
Page({
  data: {
    view: 'MINA'
  }
})
>```


#### 模板

WXML提供模板（template），可以在模板中定义代码片段，然后在不同的地方调用。


>* template wxml
>```
<template is="staffName" data="{{...staffA}}"></template>
<template is="staffName" data="{{...staffB}}"></template>
<template is="staffName" data="{{...staffC}}"></template>
<!--wxml-->
<template name="staffName">
  <view>
    FirstName: {{firstName}}, LastName: {{lastName}}
  </view>
</template>
>```
>
>*  js
>```
>Page({//进行页面的注册
  data: {
    logs: [],
    staffA: { firstName: 'Hulk', lastName: 'Hu' },
    staffB: { firstName: 'Shang', lastName: 'You' },
    staffC: { firstName: 'Gideon', lastName: 'Lin' }
  }})
>```



#### 事件


>* 事件可以绑定在组件上，当达到触发事件，就会执行逻辑层中对应的事件处理函数
>```
>事件对象可以携带额外信息，如 id, dataset, touches
>```
>


###### 事件的使用方式


>* 在组件中绑定一个事件处理函数。`bindtap`
>```
><button bindtap="switch"> Switch </button>
>```
>


# 事件详解

#### 事件分类


>* 事件分为`冒泡事件`和`非冒泡事件`：
```
1) 冒泡事件：当一个组件上的事件被触发后，该事件会向父节点传递。
2) 非冒泡事件：当一个组件上的事件被触发后，该事件不会向父节点传递。
```


###### WXML的冒泡事件列表：


<script src="https://gist.github.com/zhangkn/1a965fd0bf5cf9320f37e3aac39b02a7.js"></script>


#### 事件绑定和冒泡


>*  key、value 的形式
>```
>1) key 以bind或catch开头，然后跟上事件的类型，如bindtap、catchtouchstart。
>自基础库版本 1.5.0 起，bind和catch后可以紧跟一个冒号，其含义不变，如bind:tap、、catch:touchstart。
2) value 是一个字符串，需要在对应的 Page 中定义同名的函数。不然当触发事件的时候会报错。
>```
>

#### 事件的捕获阶段

>* 自基础库版本 1.5.0 起，触摸类事件支持捕获阶段。
>```
>需要在捕获阶段监听事件时，可以采用`capture-bind`、`capture-catch`关键字，后者将中断捕获阶段和取消冒泡阶段。
>```

###### 例子

>* 在捕获阶段中,事件到达节点的顺序与冒泡阶段恰好相反
>```wxml
><!--点击 inner view 会先后调用handleTap2、handleTap4、handleTap3、handleTap1-->
><view id="outer" bind:touchstart="handleTap1" capture-bind:touchstart="handleTap2">
  outer view
  <view id="inner" bind:touchstart="handleTap3" capture-bind:touchstart="handleTap4">
    inner view
  </view>
</view>
>```
>* 只触发handleTap2
>```
><view id="outer" bind:touchstart="handleTap1" capture-catch:touchstart="handleTap2">
  outer view
  <view id="inner" bind:touchstart="handleTap3" capture-bind:touchstart="handleTap4">
    inner view
  </view>
</view>
>```
>

#### 事件对象


>* 如无特殊说明，当组件触发事件时，逻辑层绑定该事件的处理函数会收到一个事件对象。
><script src="https://gist.github.com/zhangkn/543f4361794e0ff844388d14e3955bc5.js"></script>
>
>

###### 例子

>*  CustomEvent 自定义事件对象（继承 BaseEvent）
>```wxml
>      <button wx:if="{{!hasUserInfo && canIUse}}" open-type="getUserInfo" bindgetuserinfo="getUserInfo"> 获取头像昵称 </button>  
>```
>* 属性:detail 额外的信息
>```js
>  getUserInfo: function (e) {// 通过button.open-type.getUserInfo的方式获取数据
    console.log(e)
    app.globalData.userInfo = e.detail.userInfo
    this.setData({
      userInfo: e.detail.userInfo,
      hasUserInfo: true
    })
  }
>```












# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost miniprogram_framework_view 视图层 -t miniprogram
> #原来""的参数，需要自己加上""
```

