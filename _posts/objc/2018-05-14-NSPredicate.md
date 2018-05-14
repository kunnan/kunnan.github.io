---
layout: post
title: NSPredicate
date: 2018-05-14
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 谓词NSPredicate技术的应用
---


# 前言

>* 在进行一些数据的筛选的时候经常使用到 NSPredicate。
>```
>例如： CoreData的数据查询、按照特定条件（日期）排序的数据、从数组进行数据查询等等。
>```
>
>


# I、`filteredArrayUsingPredicate:`的使用


#### 1.1、 以一定的条件（特定日期）过滤maTemp数组，即进行大数据搜索


<script src="https://gist.github.com/zhangkn/773795958936f5b0eeb34d378196b8e8.js"></script>


#### 1.2、  城市搜索

<script src="https://gist.github.com/zhangkn/abdf770979bcb1f4057f95b0eed941fe.js"></script>


####  1.3列表的过滤--`多个过滤条件的拼接`

<script src="https://gist.github.com/zhangkn/cc639be8fa76d3bfb373aa4c33f1eb17.js"></script>

#### 1.4 数组的过滤

###### 1.4.1 字符串数组 数组元素为系统的自有类型）

<script src="https://gist.github.com/zhangkn/4c00c98d5ac38f62059dc054c6ba0e74.js"></script>

###### 1.4.2 使用谓词进行数据分组 （数组元素为 自定义类型）

<script src="https://gist.github.com/zhangkn/0756dd0d8aad7be528590ef889274c00.js"></script>


# II、`evaluateWithObject:`的应用

<script src="https://gist.github.com/zhangkn/476d3e5f94f7ab873de3f5d955e09687.js"></script>


# III 、`NSPredicate`在`Core Data`的应用

<script src="https://gist.github.com/zhangkn/039b98e4f655a23327b508ed5f63ffe9.js"></script>









 





# See Also 

>* [谓词NSPredicate技术的应用](https://blog.csdn.net/z929118967/article/details/74066170)


>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost NSPredicate 谓词NSPredicate技术的应用 -t objc
> #原来""的参数，需要自己加上""
```

