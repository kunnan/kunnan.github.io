---
layout: post
title: objc_brief_introduction
date: 2018-06-01
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: objc 简介
---


# 前言

![image](https://wx1.sinaimg.cn/large/af39b376gy1frvo5u4ldgj20yc0iwjz4.jpg)

![image](https://wx1.sinaimg.cn/large/af39b376gy1frvo2i2ar6j20g10bujtu.jpg)


# UIViewController

>* `UIViewController`就是`UIView`的大管家，负责`创建、显示、销毁`UIView，负责`监听`UIView内部的事件，负责`处理`UIView与用户的交互

# 补充

#### IBAction和IBOutlet



>* IBAction

```
1)从返回值角度上看，作用相当于void
2)只有返回值声明为IBAction的方法，才能跟storyboard中的控件进行连线
```

>* IBOutlet
```
只有声明为IBOutlet的属性，才能跟storyboard中的控件进行连线
```


#### 退出键盘的两种方式

>* resignFirstResponder
当唤起键盘的控件`(第一响应者)`调用这个方法时，就能退出键盘

>* endEditing
调用这个方法的控件内部存在第一响应者，就能退出键盘

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost objc_brief_introduction objc 简介 -t objc
> #原来""的参数，需要自己加上""
```

