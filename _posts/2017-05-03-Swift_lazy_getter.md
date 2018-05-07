---
layout:     post
title:      Swift_lazy_getter
subtitle:   lazy、getter
date:       2017-05-03
author:    
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - iOS
    - Swift
   
---


# 懒加载

<script src="https://gist.github.com/zhangkn/2a6efa8ba89f4c9d77b46e1942a66704.js"></script>

# 计算型属性

<script src="https://gist.github.com/zhangkn/97a8285567b015a8a041d29fd924304d.js"></script>


# 对比

#### 相同点

- 使用方法完全一致
- 都是用 `var` 声明

#### 不同点

- 实现原理不同

	懒加载是第一次调用属性时执行闭包进行赋值

	计算型属性是重写 `get` 方法

- 调用 `{}`的次数不同

	懒加载的闭包只在属性第一次调用时执行
	计算型属性每次调用都要进入 `{}` 中，`return` 新的值