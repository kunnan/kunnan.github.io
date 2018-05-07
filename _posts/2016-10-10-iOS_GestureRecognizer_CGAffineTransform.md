---
layout:     post
title:      iOS_GestureRecognizer_CGAffineTransform
subtitle:   手势与变形
date:       2016-10-10
author:     BY
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - iOS开发基础
---


# I、手势

>* iOS手势分为下面这几种：
>
```
- UITapGestureRecognizer（点按）
- UIPanGestureRecognizer（拖动）
- UIScreenEdgePanGestureRecognizer (边缘拖动)
- UIPinchGestureRecognizer（捏合）
- UIRotationGestureRecognizer（旋转）
- UILongPressGestureRecognizer（长按）
- ​UISwipeGestureRecognizer（轻扫）
```


#### 1、1 **离散型手势**、**连续型手势**

这些手势大都继承于**UIGestureRecognizer**类，（`UIScreenEdgePanGestureRecognizer`继承于`UIPanGestureRecognizer`类）,

需要说明的是这些手势只有一个是**离散型手势**，那就是`UITapGestureRecognizer`，一旦识别就无法取消，而且只会调用一次手势操作事件。

换句话说其他手势是**连续型手势**，而连续型手势的特点就是：会多次调用手势操作事件，而且在连续手势识别后可以取消手势。

>* 从下图可以看出两者调用操作事件的次数是不同的：

![](http://ww3.sinaimg.cn/large/006tNc79gw1fb0neee6mlj30dw0aldgf.jpg)



#### 1.2 手势类有着以下共同的方法:



<script src="https://gist.github.com/zhangkn/5ea8ac0d5a4fef722df0c3b392153a06.js"></script>



#### 1.3  UITapGestureRecognizer（点按）


<script src="https://gist.github.com/zhangkn/9d8dc2f553347fce42eaeea82685fb78.js"></script>


#### 1.4 UIPanGestureRecognizer（拖动）

<script src="https://gist.github.com/zhangkn/ab16c101f9588ea4af6173fa7ee2cae4.js"></script>


#### 1.5 UIScreenEdgePanGestureRecognizer (边缘拖动)

<script src="https://gist.github.com/zhangkn/9f2b9cf1af4780e8648921beb6ed122d.js"></script>


#### 1.6 UIPinchGestureRecognizer（捏合）
<script src="https://gist.github.com/zhangkn/db8042312593d0b7f0cd7bbfc7e23522.js"></script>


#### 1.7 UIRotationGestureRecognizer（旋转）

<script src="https://gist.github.com/zhangkn/806e46821838788d1389069ba8feb5a6.js"></script>

#### 1.8 UILongPressGestureRecognizer（长按）

<script src="https://gist.github.com/zhangkn/1522507a12b68ddf16a579124b0e1616.js"></script>

# 2. 变形

---
iOS的变形指的是图片的旋转、平移和缩放。这些变形可以和上面介绍的手势结合，完成许多变形操作。

>* **CGAffineTransform**为一个结构体：
><script src="https://gist.github.com/zhangkn/7527c02c7135d624ddeb5267b1d99230.js"></script>
>* 对iOS控件进行变形实际就是对控件`transform`属性进行操作。


#### 2.1 缩放

>* 缩放操作
><script src="https://gist.github.com/zhangkn/b4c5f293f3ead3ec6a7d11b2127d4cd8.js"></script>
>

#### 2.2 平移

<script src="https://gist.github.com/zhangkn/8835e4c7369b2bbe5b4de057141cb95f.js"></script>


#### 2.3 旋转


<script src="https://gist.github.com/zhangkn/5a09b0fefcca6151a41993ec19ffd317.js"></script>



# 3.手势结合变形
---
>* 手势结合变形就是通过手势对控件变形处理。
><script src="https://gist.github.com/zhangkn/0129f50f1369f78aeb606b6b8ea38220.js"></script>
><script src="https://gist.github.com/zhangkn/97488c0ee3e1e58260411c54b2771130.js"></script>
	
	
# 4.在storyboard中添加手势


#### 4.1 使用方法：

1. 直接将手势控件拖到要添加的视图上

	![](http://ww3.sinaimg.cn/large/006tNc79gw1fb0ja1f8fnj30f206nwev.jpg)
	
2. 关联手势事件

	![](http://ww2.sinaimg.cn/large/006tNc79gw1fb0jaxllv6j30ol0be77b.jpg)

3. 设置手势属性

	![](http://ww2.sinaimg.cn/large/006tNc79gw1fb0jc5mon3j307c06ydgd.jpg)
	
注意：若想同时识别多个手势，方法和上面相同，遵循协议，实现方法，设置代理，不过代理可以手动关联。

![](http://ww4.sinaimg.cn/large/006tNc79gw1fb0jokip2vj30ej0aq3zz.jpg)
