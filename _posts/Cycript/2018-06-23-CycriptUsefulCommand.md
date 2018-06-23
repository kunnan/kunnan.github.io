---
layout: post
title: CycriptUsefulCommand
date: 2018-06-23
tag: Cycript
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 常用的cycript命令、分析思路
---


# [前言](http://iphonedevwiki.net/index.php/Cycript)


# I 、Powerful private methods

>* Powerful private methods

```
_ivarDescription
_shortMethodDescription
nextResponder
_autolayoutTrace
recursiveDescription
_methodDescription
```

#### 1、1 定位`view`

>* ViewControlle(Shortcut to find the ViewController’s class name on the keyWindow): _printHierarchy
```
[[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
```

>* cy# [#0x181009f0 nextResponder]

>* 打印出当前界面的view层级
```
cy# UIApp.keyWindow.recursiveDescription().toString()
```
or 
```
 [UIWindow keyWindow].recursiveDescription().toString()
```

#### 1.2  定位 `按钮地址`


>*  `[UIWindow keyWindow].recursiveDescription().toString()` 展开views结构
```
[UIWindow keyWindow].recursiveDescription().toString()
```

>* [按钮文字unicode转码](http://tool.chinaz.com/tools/unicode.aspx)

```
\u4e8c\u7ef4\u7801\u6536\u6b3e 进行搜索找到对应按钮
```

#### 1.3 定位`对象属性`

>* _ivarDescription: Prints all names and values of instance variables of a specified object
```
cy# [#0x5822600 _ivarDescription].toString()
```


# II、 快速定位按钮对应的`allTargets `、`allControlEvents `、 `actionsForTarget `


#### 2.1  获取actionsForTarget的步骤

>* 2.1.1 获取allTargets
```
cy# [[#0x170bab80 allTargets] allObjects][0]
#"<WCPayOfflinePayViewController: 0x1642b200>"
```
>* 2.1.2 获取 actions
```
cy# [#0x170bab80 actionsForTarget:[[#0x170bab80 allTargets] allObjects][0] forControlEvent:[#0x170bab80 allControlEvents]]
@["receiveMoneyBtnPress:"]
```




#### 2.2  验证`执行方法`

>* objc_msgSend
```
            objc_msgSend(vc, @selector(initView));
```
>* `[]`
```
cy# [[[#0x170bab80 allTargets] allObjects][0] receiveMoneyBtnPress:nil]
```
>* valueForKey
```
cy# [[#0x183d6e00 valueForKey:@"m_delegate"] WCPayFacingReceiveChangeToFixedAmount]
```
#### 2.3 other 

>* 2.3.1 `验证是否找对了view`
```
 cy# [#0x189ce7e0 setHidden:0] 
```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost CycriptUsefulCommand 常用的cycript命令、分析思路 -t Cycript
> #原来""的参数，需要自己加上""
```

>* [CycriptTricks](https://kunnan.github.io/2018/04/20/CycriptTricks/)
>* [UIButton_sendActionsForControlEvents](https://kunnan.github.io/2018/06/08/UIButton_sendActionsForControlEvents/)
>* [csdn](https://blog.csdn.net/z929118967/article/details/78309400)

