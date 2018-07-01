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

使用cy 分析应用：查看界面、查找关键函数、验证代码执行效果

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

#### 1.0   定位特定的class

> * choose():get an array of existing objects of a certain class(当前堆栈中查找到特定类的对象数据)

```
cy# choose(UIButton).toString()
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

> * _autolayoutTrace:基于layout展示View架构
>
>   ```
>   [[UIApp keyWindow] _autolayoutTrace].toString()
>   ```
>
>   ```
>   cy# [[UIWindow keyWindow] _autolayoutTrace].toString()
>   ```



#### 1.2  定位 `按钮地址`


>*  `[UIWindow keyWindow].recursiveDescription().toString()` 展开views结构
```
[UIWindow keyWindow].recursiveDescription().toString()
```

>* [按钮文字unicode转码](http://tool.chinaz.com/tools/unicode.aspx)

```
\u4e8c\u7ef4\u7801\u6536\u6b3e 进行搜索找到对应按钮
```

> * 定位UIButton class 对应的对象，返回的是数组
>
>   ```
>   cy# choose(UIButton).toString() 
>   ```
>
>   

#### 1.3 定位`对象属性`

>* _ivarDescription: Prints all names and values of instance variables of a specified object
```
cy# [#0x5822600 _ivarDescription].toString()
```

#### 1.4 定位一些基本信息和路径 

> 
>
> * 使用`?exit` 或者`control+d` 进行退出
>
> * bundleIdentifier
>
>   ```
>   [[NSBundle mainBundle] bundleIdentifier]
>   ```
>
> * 沙盒
>
>   ```
>   [[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]
>   ```
>
>   ```
>   FOUNDATION_EXPORT NSString *NSHomeDirectory(void);
>   cy# [NSHomeDirectory()]
>   [@"/var/mobile/Containers/Data/Application/93E1B7BE-8916-411C-9228-35B9FD8DA825"]
>   ```
>
>   

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

>* 2.1.3 NSLog: `allTargets `,`actionsForTarget`
>```       
>NSLog(@"allTargets: %@ actionsForTarget :%@",[normalRedEnvelopesButton allTargets],[normalRedEnvelopesButton actionsForTarget:[[normalRedEnvelopesButton allTargets] allObjects][0]forControlEvent:[normalRedEnvelopesButton allControlEvents]]);
>```
```

#### 2.2  验证`执行方法`

>* `objc_msgSend` : 好处： 不需要声明方法，即可执行使用
​```objc
            objc_msgSend(vc, @selector(initView));//  执行实例方法
```
    objc_msgSend(objc_getClass("FTSContactMgr"), sel_registerName("setupgroupsyncdata"));//执行类方法
```
    id data = objc_msgSend(MainFrameLogic, @selector(getSessionInfoByContact:), contact);
```
>* `[]`
```
cy# [[[#0x170bab80 allTargets] allObjects][0] receiveMoneyBtnPress:nil]
```
>* valueForKey
```
cy# [[#0x183d6e00 valueForKey:@"m_delegate"] WCPayFacingReceiveChangeToFixedAmount]
```

>* sendActionsForControlEvents

```
    //    UIView  *normalRedEnvelopesButton  [self valueForKey:@"normalRedEnvelopesButton"];
//        [normalRedEnvelopesButton sendActionsForControlEvents:UIControlEventTouchUpInside];// OnMakeWCRedEnvelopesButtonClick
```
# III 、 other 


#### 3.2 nextResponder 有助于分析控件的层级结构

#### 3.2 获取属性

>* _ivarDescription
>* [self valueForKey:@"m_delegate"]
>* class_getInstanceVariable
>```
>    Ivar inputToolViewIvar = class_getInstanceVariable(objc_getClass("BaseMsgContentViewController"), "_inputToolView");
    id inputToolView = object_getIvar(self, inputToolViewIvar);
>```

#### 3.3 setHidden
>* 3.3.1 `验证是否找对了view`
```
 cy# [#0x189ce7e0 setHidden:0] 
```





# See Also 

>* 微信资助二维码
>![image](https://wx4.sinaimg.cn/large/af39b376gy1fsm4fqug79j207s07s3yc.jpg)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost CycriptUsefulCommand 常用的cycript命令、分析思路 -t Cycript
> #原来""的参数，需要自己加上""
```

>* [CycriptTricks](https://kunnan.github.io/2018/04/20/CycriptTricks/)
>* [UIButton_sendActionsForControlEvents](https://kunnan.github.io/2018/06/08/UIButton_sendActionsForControlEvents/)
>* [csdn](https://blog.csdn.net/z929118967/article/details/78309400)


```

```