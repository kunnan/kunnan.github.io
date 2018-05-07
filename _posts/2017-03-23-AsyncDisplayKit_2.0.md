---
layout:     post
title:      AsyncDisplayKit_2.0
subtitle:   AsyncDisplayKit Tutorial:Getting Started
date:       2017-03-23
author:    
header-img: img/post-bg-iWatch.jpg
catalog: true
tags:
    - iOS
   
   
   
---


# 前言


>* [**AsyncDisplayKit**](http://asyncdisplaykit.org/) 是一个UI框架，尽可能缓解主线程的压力.


####  一些主线程开销较大的任务

- **计算尺寸和布局**：比如  `-heightForRowAtIndexPath:`，或者在UILbel中调用 `-sizeThatFits` 以及 `AutoLayout‘s`布局计算。
- **图像解码**：想要在一个 image view 中使用 `UIImage`，首先要进行解码。
- **绘图**：复杂的文本以及手动绘制渐变和阴影。
- **对象生命周期**：创建，操纵和销毁系统对象（创建一个UIView）



# 开始

>* 首先，[下载初始项目](https://koenig-media.raywenderlich.com/uploads/2016/12/AsyncDisplayKit-Starter-4.zip)。

>* 该项目使用 [CocoaPods](https://cocoapods.org/) 来拉入AsyncDisplayKit。


# ASDisplayNode 简介

`ASDisplayNode` 是ASDK的核心类，它只是一个类似于 MVC 中的 “View” 一样的`UIView` 或 `CALayer`。



记住，iOS应用程序中的所有在屏幕上的显示都通过`CALayer`对象表示的。`UIViews` 创建并且拥有一个底层的 `CALayer`，并为他们添加触摸处理和其他交互功能。`UIView` 并不是 `CALayer` 的子类，而是相互环绕，扩展其功能。


![](https://koenig-media.raywenderlich.com/uploads/2016/03/view-layer-480x229.png)

>* 通常由 Node 创建的一个常规的view，其创建和配置都在行队列中执行，并且异步渲染。

![](https://koenig-media.raywenderlich.com/uploads/2016/03/node-view-layer-480x161.png)


# 节点容器（The Node Containers）



- **ASViewController**：一个 `UIViewController` 的子类，允许你提供要管理的 Node。
- **ASCollectionNode** and **ASTableNode**：Node 等效于 `UICollectionView` 和 `UITableView`，其子类实际上保留在底层。
- **ASPagerNode**:一个`ASCollectionNode`的子类，提供极好的滑动性能相比与 `UIKit` 的 `UIPageViewController` 来说。



# TableNode


#### 将 TableView 替换为 TableNode

<script src="https://gist.github.com/zhangkn/852b4e09975b38fabe789930c2ae8ef8.js"></script>






#### 设置  TableNode 的 DataSource & Delegate

<script src="https://gist.github.com/zhangkn/00548121abf24ed200ee36f13ef59da3.js"></script>


#### 遵循 ASTableDataSource

<script src="https://gist.github.com/zhangkn/8b2be29f02e00f6fc961e16d464348f3.js"></script>


#### 遵循 ASTableDelegate

<script src="https://gist.github.com/zhangkn/bb26dc48a82b0555c4fb07e2e686cccd.js"></script>

# 无限滚动


>* 预先确定好你需要加载的页数。
><script src="https://gist.github.com/zhangkn/6889a09dd9a2c0b146b5d18134719403.js"></script>

![](https://koenig-media.raywenderlich.com/uploads/2016/06/InfiniteScrollingGif.gif)


# 智能预加载



>*  `ASRangeController`
>* 在每个容器类中，所有包含的 node 都有一个接口状态的概念。在任何给定的时间，一个 node 可以是下面的任意组合：

- **Preload Range（预载范围）**：通常最远的范围从可见区域。这是当cell的每个 subNode （例如ASNetworkImageNode） 的内容从外源加载，例如API和本地缓存。这与批量获取时，使用用模型对象代表cell本身形成对比。
- **Display Range（显示范围）**：在这里进行显示任务，例如文本绘制和进行图像解码。
- **Visible Range（可见范围）**：此时，node 至少有一个像素在屏幕上。

![](https://koenig-media.raywenderlich.com/uploads/2016/12/preloadingRanges-small.png)

这些范围也适用于 **screenfuls** 的度量，并且可以使用 `ASRangeTuningParameters` 属性轻松调整。


# Node接口的状态回调


####  Node 命名



	
>* 给 `cardNode` 添加一个 `debugName`：

```objc
//回到代码`-tableNode:nodeBlockForRowAtIndexPath:`
cardNode.debugName = [NSString stringWithFormat:@"cell %zd", indexPath.row];
```
#### 观察 Cells

进入 `CardNode_InterfaceCallbacks.m` 中，你可以找到六种追踪 node 在 ranges 中的状态的方法。

