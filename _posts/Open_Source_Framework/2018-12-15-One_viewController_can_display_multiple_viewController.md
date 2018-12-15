---
layout: post
title: One_viewController_can_display_multiple_viewController
date: 2018-12-15
tag: Open_Source_Framework
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: A custom PageViewController for iOS with the tab bar control at the top
---





# 前言



最近一个需要在一个View 展示切换管理几个样式风格各异的View。因此写了这个模版



#### Example diagram



![image](https://wx1.sinaimg.cn/large/af39b376gy1fy7h5yfu0pj20ku112n3s.jpg)

#  [code ](https://github.com/zhangkn/KNSegmentedViewControllers)



```objc
NSArray *tmp_itemArray = @[@"step1\n基本信息",@"step2\n信息",@"step3\n信息",@"step4\n照片"];

CGFloat leftEdg = 15;

CGFloat  tmptopButtonWidth = ([[UIScreen mainScreen] bounds].size.width-leftEdg*2)/tmp_itemArray.count;

CGFloat height = tmptopButtonWidth*(79/168.0);// 168/79 = w/h

TopButtonView  *tmp = [[TopButtonView alloc] initWithFrame:CGRectMake(leftEdg, leftEdg, [[UIScreen mainScreen] bounds].size.width-leftEdg*2, height) withTitleArray:tmp_itemArray];

```



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost One_viewController_can_display_multiple_viewController A custom PageViewController for iOS with the tab bar control at the top -t Open_Source_Framework
> #原来""的参数，需要自己加上""
```

