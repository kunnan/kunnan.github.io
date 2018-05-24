---
layout: post
title: component
date: 2018-05-22
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 组件是视图层的基本组成单元,通过组合这些基础组件进行快速开发
---


# component

```xml
<tagname property="value">
  Content goes here ...
</tagname>
```

# 视图容器

#### view

```xml
  <view class="flex-wrp" style="flex-direction:row;">
  </view>
```


#### scroll-view

使用竖向滚动时，需要给<scroll-view/>一个固定高度，通过 WXSS 设置 height。

```xml
  <scroll-view scroll-y style="height: 200px;" bindscrolltoupper="upper" bindscrolltolower="lower" bindscroll="scroll" scroll-into-view="{{toView}}" scroll-top="{{scrollTop}}">
  </scroll-view>
```



#### `swiper` 滑块视图容器


###### swiper-item

仅可放置在`<swiper/>`组件中，宽高自动设置为100%。

#### [movable-view](https://developers.weixin.qq.com/miniprogram/dev/component/movable-view.html)


#### cover-view


#### cover-image


# 基础内容

#### icon

```xml
    <icon type="success" size="{{item}}"/>
```

#### text 

`<text/>` 组件内只支持` <text/>` 嵌套。

#### rich-text

#### [progress 进度条](https://developers.weixin.qq.com/miniprogram/dev/component/progress.html)


# 表单组件

#### button


<script src="https://gist.github.com/zhangkn/bc227aee4eb7e9b0cb693dd4a5549422.js"></script>


>* 打开客服会话
 
```xml
<button class="btn" open-type="contact">领取</button>
```

>* 触发用户转发

```
        <button class="btn" open-type="share"/>
```

>* 获取用户信息，可以从bindgetuserinfo回调中获取到用户信息

```
      <button wx:if="{{ss}}" class="xx-btn" open-type="getUserInfo" lang="zh_CN" bindgetuserinfo="ss"/>
```


>* 打开授权设置页


```
<button open-type="openSetting">打开授权设置页</button>
```


```
    wx.openSetting({
      success: res => {
        wx.hideLoading()
      },
      fail: res => {
        wx.hideLoading()
      }
    })
```


#### checkbox-group

多项选择器，内部由多个checkbox组成。

#### checkbox

```xml
<checkbox-group bindchange="checkboxChange">
  <label class="checkbox" wx:for="{{items}}">
    <checkbox value="{{item.name}}" checked="{{item.checked}}"/>{{item.value}}
  </label>
</checkbox-group>
```


#### form




# 新增 remove-console
>* package.json
>
```
+    "babel-plugin-transform-remove-console": "^6.9.1",
```

>*  wepy.config.js
>
```js
module.exports.compilers.babel.plugins.push('transform-remove-console')
```

# 字符串操作

>* 去除第一个字符
>```
>    let hashData = BW.createHash(BW.reqstr.Gamecontinue.substring(1))
>```


# See Also 

```
+       "libVersion": "2.0.6", 这个库才可以获取群ID
```

>* [es6](http://es6.ruanyifeng.com/#docs/intro)
>

>* font-family
>
```css
font-family: "Helvetica Neue", Helvetica, Arial, "PingFang SC", "Hiragino Sans GB", "Heiti SC", "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif;
```

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost component 组件是视图层的基本组成单元,通过组合这些基础组件进行快速开发 -t miniprogram
> #原来""的参数，需要自己加上""
```

