---
layout:     post
title:      IB_DESIGNABLE&IBInspectable
subtitle:   以`设置控件圆角和描边为例`谈谈IB_DESIGNABLE、IBInspectable
date:       2016-12-01
author:    
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - Swift
---

# 前言


>* 常规通过IB设置控件（设置圆角、描边、文本控件添加本地化字符串）的缺点
>```
>有一些属性没有暴露在 IB 的设置面板中
>```


#### IB设置控件的属性的常用方法

>* 从Storyboard关联出属性，然后再对属性进行代码处理。
><script src="https://gist.github.com/zhangkn/804ec755c610a5fecb488282ab2655f7.js"></script>


>* 选中控件，然后在Runtime Attributes框中输入对应的`Key`与`Type`与`Value`,这样程序在运行时就会通过KVC为你的控件属性进行赋值。
>![](http://ww4.sinaimg.cn/large/7853084cgw1fabg89aeqkj207b08j74y.jpg)
>

#### 本文的主要内容

objc 和swift 两个语言如何利用IBInspectable，来提高开发效率


# I 、IB_DESIGNABLE、IBInspectable 实现控件属性的关联


使用 `@IBInspectable` 在 IB 面板中添加自定义属性，利用 `@IBInspectable` 减少代码设置.



#### 1.0 实现方法

>* 创建UIView的分类，使用`IBInspectable`+ `IB_DESIGNABLE`关键字
><script src="https://gist.github.com/zhangkn/a84c12311253de0929cfa932c13dbb5f.js"></script>

#### 1.1 使用方法：xib上直接设置属性：cornerRadius、borderWidth、borderColor


>* 在控件的右边栏` Attributes inspector` 选项卡将会显示圆角和描边的属性设置
>![](http://ww4.sinaimg.cn/large/7853084cgw1facfqugjtbj20mp07v401.jpg)
>

#### 1.2 实时动态显示设置效果的方法

>* 写一个空白的子类设置在Customer Class 中
><script src="https://gist.github.com/zhangkn/89eb967d5174eddbc0af324a0a1cfc71.js"></script>
>
>

#### 1.3 [code](https://github.com/kunnan/KNIBInspectable)

>* [KNIBInspectable/KNIBInspectableEx](https://github.com/kunnan/KNIBInspectable/tree/master/KNIBInspectable/KNIBInspectableEx)
>

# II、在Swift中使用IBInspectable


使用在对应的 view 中添加` @IBInspectable` 的 `extension `方法来解决

#### 2.0 扩展语法

>* 使用关键字 extension 来声明扩展：
>```swift
>extension SomeType {
    // 为 SomeType 添加的新功能写到这里
}
>```



#### 2.1 设置圆角、描边、本地化字符串的key

>* [code](https://github.com/kunnan/KNSwiftIBInspectable/blob/master/KNSwiftIBInspectable/IBInspectable/UIViewExt.swift)
>![](https://ws1.sinaimg.cn/large/006tKfTcgy1fr2w2usos4j30ei08i0su.jpg)


#### 2.2 利用 `@IBDesignable` 在 IB 中实时显示 `@IBInspectable` 的样式

>* [code](https://github.com/kunnan/KNSwiftIBInspectable/blob/master/KNSwiftIBInspectable/IBInspectable/KNIBDesignableView.swift)
>![](https://ws3.sinaimg.cn/large/006tKfTcgy1fr2w8odusmj31cc08oaah.jpg)


#### 2.3  IBInspectable can be used with the below types,
- `Int`
- `CGFloat`
- `Double`
- `String`
- `Bool`
- `CGPoint`
- `CGSize`
- `CGRect`
- `UIColor`
- `UIImage`

#### 2.4 [code](https://github.com/kunnan/KNSwiftIBInspectable)


# see also

> -  [《再看关于 Storyboard 的一些争论》](https://onevcat.com/2017/04/storyboard-argue/)
> - [《@IBDesignable and @IBInspectable in Swift 3》](https://medium.com/@Anantha1992/ibdesignable-and-ibinspectable-in-swift-3-702d7dd00ca)
> 
>* [21_Extensions.html](http://wiki.jikexueyuan.com/project/swift/chapter2/21_Extensions.html)

>* [关于`IBInspectable`与`IB_DESIGNABLE`的使用详情:谈不完美的IBDesignable/IBInspectable可视化效果编程》](http://www.jianshu.com/p/a90e44ba1f2b)
>