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

#  install frida-server through Cydia

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

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost install_frida_on_device_and_mac Frida的安装 -t frida
> #原来""的参数，需要自己加上""
```

