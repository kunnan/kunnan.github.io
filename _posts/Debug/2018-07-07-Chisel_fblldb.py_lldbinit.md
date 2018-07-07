---
layout: post
title: Chisel_fblldb.py_lldbinit
date: 2018-07-07
tag: Debug
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Chisel is a collection of LLDB commands to assist debugging iOS apps.
---

# 前言



lldb 会默认从`~/.lldbinit `加载自定义脚本。新增command script之后，重启Xcode，或者直接在lldb交互模式下输入`command source ~/.lldbinit`来加载脚本。



# 0、lldb 基础 

lldb命令的格式如下 ：

```
<noun> <verb> [-options [option-value]] [argument [argument..]]
```

#### 1.1基础

###### *help*

在控制台输入`help`，显示控制台支持的lldb命令

###### apropos

搜索相关的命令信息。

###### *print* 缩写`p`

print是 `expression --` 的缩写

> - print 可以指定格式打印 `默认 p` `十六进制 p/x`、`二进制 p/t`
>
> ```
> (lldb) p 16
> 16
> (lldb) p/x 16
> 0x10
> (lldb) p/t 16
> 0b00000000000000000000000000010000
> (lldb) p/t (char)16
> 0b00010000
> ```

你也可以使用 p/c 打印字符，或者 p/s 打印以空终止的字符串  p/d打印ACRSII(译者注：以 '\0' 结尾的字符串)。

> - [点击查看更多清单](https://sourceware.org/gdb/onlinedocs/gdb/Output-Formats.html)

###### *po*

打印对象，是 `e -o --`的缩写

```
po [0x116e534e0 _ivarDescription]
```





#  I 、使用python调用lldb的API



###### 1.1 [使用python调用lldb的API](https://github.com/zhangkn/chisel)

两个开源库：`[DerekSelander](https://github.com/DerekSelander)/**LLDB**`、`Chisel`



> - [Chisel is a collection of LLDB commands to assist debugging iOS apps. ](https://github.com/zhangkn/chisel)
>
>   ```
>   brew update
>   brew install chisel
>   ```
>
>   ```
>   Add the following line to ~/.lldbinit to load chisel when Xcode launches:
>     command script import /usr/local/opt/chisel/libexec/fblldb.py
>   ```
>
>   
>
> - [A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions ](https://github.com/DerekSelander/LLDB)
>
>   ```
>   Add the following command to your ~/.lldbinit file: command script import /path/to/lldb_commands/dslldb.py
>   ```
>
>   



> - ## Commands
>
>   > * pviews 对应私有API：`[[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()`
>
>   ```
>   (lldb) pviews
>   <iConsoleWindow: 0x10b093b70; baseClass = UIWindow; frame = (0 0; 320 568); gestureRecognizers = <NSArray: 0x10a7a2ce0>; layer = <UIWindowLayer: 0x10b07f110>>
>   ```
>
>   > * pvc 对应私有API：`[UIWindow keyWindow].recursiveDescription().toString()`
>
>   ```
>   (lldb) pvc
>   <MainTabBarViewController 0x10b8b2000>, state: appeared, view: <UILayoutContainerView 0x10b32dde0>
>      | <MMUINavigationController 0x10c05c200>, state: appeared, view: <UILayoutContainerView 0x10b400750>
>      |    | <NewMainFrameViewController 0x10b8c5000>, state: appeared, view: <MMUIHookView 0x10a7a74f0>
>      | <MMUINavigationController 0x10ba78400>, state: disappeared, view: <UILayoutContainerView 0x1194e3080> not in the window
>      |    | <ContactsViewController 0x10b99c400>, state: disappeared, view:  (view not loaded)
>      | <MMUINavigationController 0x10c1e6400>, state: disappeared, view: <UILayoutContainerView 0x1194631e0> not in the window
>      |    | <FindFriendEntryViewController 0x10c1e9a00>, state: disappeared, view:  (view not loaded)
>      | <MMUINavigationController 0x10b891e00>, state: disappeared, view: <UILayoutContainerView 0x116ea64f0> not in the window
>      |    | <MoreViewController 0x10c1cf400>, state: disappeared, view:  (view not loaded)
>   ```
>
>   > * **presponder** 对应私有API：`nextResponder`
>
>   ```
>   (lldb) presponder 0x116c695a0
>   <MMBadgeView: 0x116c695a0; baseClass = UIImageView; frame = (283 1; 20 20); opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x116c69220>>
>      | <MMTabBar: 0x10b337140; baseClass = UITabBar; frame = (0 519; 320 49); autoresize = W+TM; tintColor = UIExtendedSRGBColorSpace 0.2 0.65098 1 1; gestureRecognizers = <NSArray: 0x10b3e13a0>; layer = <CALayer: 0x10b3377b0>>
>      |    | <UILayoutContainerView: 0x10b32dde0; frame = (0 0; 320 568); autoresize = W+H; layer = <CALayer: 0x10b095910>>
>      |    |    | <MainTabBarViewController: 0x10b8b2000>
>      |    |    |    | <iConsoleWindow: 0x10b093b70; baseClass = UIWindow; frame = (0 0; 320 568); gestureRecognizers = <NSArray: 0x10a7a2ce0>; layer = <UIWindowLayer: 0x10b07f110>>
>      |    |    |    |    | <UIApplication: 0x10b05be70>
>      |    |    |    |    |    | <MicroMessengerAppDelegate: 0x10a78a770>
>   ```
>
>   > * 直接打开UIImage对应的图片 :  **visualize 0x10a7ee030**  `Open a `UIImage`, `CGImageRef`, `UIView`, `CALayer`, `NSData` (of an image), `UIColor`, `CIColor`, or `CGColorRef` in Preview.app on your Mac` 
>   >
>   >   ![image](https://wx4.sinaimg.cn/large/af39b376gy1ft15pjzimuj20l909baca.jpg)

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Chisel_fblldb.py_lldbinit Chisel is a collection of LLDB commands to assist debugging iOS apps. -t Debug
> #原来""的参数，需要自己加上""
```

