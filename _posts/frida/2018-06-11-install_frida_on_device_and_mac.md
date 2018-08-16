---
layout: post
title: install_frida_on_device_and_mac
date: 2018-06-11
tag: frida
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Frida的安装
---

# 前言

> * Frida是跨平台的注入工具，通过注入js于native的js引擎进行交互，从而执行native的代码进行hook和动态调用
> * 使用Frida调试分析Windows、macOS、Linux、Android、iOS软件，现在时下再流行不过了。我常用它来提高逆向的效率
>   * https://zhangkn.github.io/2017/12/codeshare.frida.re/
>   * 本文重点推荐使用[frida-ios-dump-master](https://github.com/zhangkn/frida-ios-dump)，而非dumpdecrypted.dylib

 



#   I 、install frida-server through Cydia

为了使用Frida，需要在Mac和iOS上面分别安装Frida。

>* 1、 install frida-server through Cydia:Start Cydia and add Frida's repository by navigating to Manage -> Sources -> Edit -> Add and entering `https://build.frida.re`


```
-rwxr-xr-x 1 root wheel 11292672 Dec 14 00:54 /usr/sbin/frida-server*
-rw-r--r-- 1 root wheel 779 Dec 14 00:54 /Library/LaunchDaemons/re.frida.server.plist
```


>* 2、mac里面python自带easy_install pip
pip是python的包管理工具
```
devzkndeMacBook-Pro:site-packages devzkn$ sudo easy_install pip
```
>* 3、install the Frida Python package on your host machine
```
devzkndeMacBook-Pro:site-packages devzkn$ sudo -H pip install frida
```


>* 4、Connect your device via USB and make sure that Frida work
```
-U, --usb             connect to USB device
-a, --applications    list only applications
-i, --installed       include all installed applications
devzkndeMacBook-Pro:site-packages devzkn$  frida-ps -Uai
PID  Name          Identifier                 
---  ------------  ---------------------------
904  Cydia         com.saurik.Cydia           
856  微信            com.tencent.xin            
858  邮件            com.apple.mobilemail       
App Store     com.apple.AppStore         
```
>* 5、 upgrade frida
```
devzkndeMacBook-Pro:bin devzkn$ sudo pip install --upgrade frida --ignore-installed six
```


# See Also 

>* [https://zhangkn.github.io/2017/12/codeshare.frida.re/](https://zhangkn.github.io/2017/12/codeshare.frida.re/)
>
>* Frida环境的搭建可以看下[这篇文章](https://zhangkn.github.io/2017/12/frida/#gsc.tab=0)
>
>* [dumpdecrypted](https://zhangkn.github.io/2017/12/dumpdecrypted/)
>
>  * 本文重点推荐使用[frida-ios-dump-master](https://github.com/zhangkn/frida-ios-dump)，而非dumpdecrypted.dylib。
>
>  * [Frida还能玩脱壳]( https://github.com/dstmath/frida-unpack)
>
>  * [A universal memory dumper using Frida](https://github.com/zhangkn/fridump)
>
>  * [Yet another frida based iOS dumpdecrypted](https://github.com/ChiChou/frida-ipa-dump)
>
>     
>
>* [使用Frida动态分析软件，你想要的姿势全部在这里](https://mp.weixin.qq.com/s/s_zR1Gq_MNj17vDVDuoDKg)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost install_frida_on_device_and_mac Frida的安装 -t frida
> #原来""的参数，需要自己加上""
```

