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

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost miniprogram_framework_view 视图层 -t miniprogram
> #原来""的参数，需要自己加上""
```

