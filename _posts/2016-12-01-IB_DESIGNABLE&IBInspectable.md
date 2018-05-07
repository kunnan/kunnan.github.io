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
---

# 前言


>* 从Storyboard关联出属性，然后再对属性进行代码处理。
><script src="https://gist.github.com/zhangkn/804ec755c610a5fecb488282ab2655f7.js"></script>


>* 选中控件，然后在Runtime Attributes框中输入对应的`Key`与`Type`与`Value`,这样程序在运行时就会通过KVC为你的控件属性进行赋值。
>![](http://ww4.sinaimg.cn/large/7853084cgw1fabg89aeqkj207b08j74y.jpg)


# I 、IB_DESIGNABLE、IBInspectable 实现控件属性的关联


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

#### [code](https://github.com/kunnan/KNIBInspectable)

>* [KNIBInspectable/KNIBInspectableEx](https://github.com/kunnan/KNIBInspectable/tree/master/KNIBInspectable/KNIBInspectableEx)
>

# see also

>* [关于`IBInspectable`与`IB_DESIGNABLE`的使用详情:谈不完美的IBDesignable/IBInspectable可视化效果编程》](http://www.jianshu.com/p/a90e44ba1f2b)
>