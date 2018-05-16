---
layout: post
title: dismissViewControllerAnimatedUIAlertController
date: 2018-05-15
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: UIIAlertController的自动消失
---

# 消除 sb 的UIAlertController

>* hook sb
>```plist
>{ Filter = { Bundles = ( "com.tencent.xin","com.apple.springboard","com.apple.Preferences"); }; }
>```
>
>* hook all app 
>```
>com.apple.UIKit
>Filter = {Bundles = ("com.apple.UIKit");};
>```
>
>*code
><script src="https://gist.github.com/zhangkn/8bd6e511ef5d03442083f4cdd91e3b9f.js"></script>


# tweak 代码示范`dismissViewControllerAnimatedUIAlertController`

>* [dismissViewControllerAnimatedUIAlertController](https://github.com/kunnan/KNdismissViewControllerAnimatedUIAlertController)
>
>
>* 封装前后的代码对比
><script src="https://gist.github.com/zhangkn/df5f46b732fb01e9d9cf1cdcbfc476e0.js"></script>



# 基础知识

####  %ctor 进行分组初始化

>* NSBundle.mainBundle.bundleIdentifier
>```objc
%ctor
{
	if ([NSBundle.mainBundle.bundleIdentifier isEqual:@"com.apple.springboard"])
	{
		[[TIDEBioServer sharedInstance] setUpForMonitoring];
	}
}
```
><script src="https://gist.github.com/zhangkn/cb676bf5f1abebc604a1f38a2d11fa92.js"></script>



>* processName
><script src="https://gist.github.com/zhangkn/352d0a3446ac455458a2264d8780b6ce.js"></script>
>


>* [Makefile的举例](https://github.com/efrederickson/PowerSaveMode/blob/master/Makefile)
>```
>https://github.com/kunnan/PowerSaveMode/blob/master/Makefile
>```
><script src="https://gist.github.com/zhangkn/5aa758cb24c26984402610cdcf82c605.js"></script>
>
>

# 适配

>* `iOS10-Runtime-Headers/PrivateFrameworks/SpringBoardUI.framework/_SBAlertController.h `适配
>```
>*** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'Unexpectedly dismissed alert controller (<_SBAlertController: 0x1068e73b0; title: "未安装 SIM 卡">), please file a radar to PEP SpringBoard about this bad citizen: <SBSIMLockAlertItem: 0x17407a780; alertController: <_SBAlertController: 0x1068e73b0; title: "未安装 SIM 卡">; occluded: NO>'
>```
>* code
>```objc
>	if (/* condition */ [self isKindOfClass:%c(_SBAlertController)])
	{
		/* code */
		// 
				NSLog(@" 暂时不处理ios10 sb 弹框 title :%@ message:%@",AlertHeader,[self message]);
		return;
	}
>```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost dismissViewControllerAnimatedUIAlertController UIIAlertController的自动消失 -t iosre
> #原来""的参数，需要自己加上""
```

