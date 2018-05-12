---
layout: post
title: iosre_tool
date: 2018-05-11
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 逆向常用工具
---

# 前言

>* [code: KNiosreTool](https://github.com/kunnan/KNiosreTool)
>```
>存储一些逆向分析的工具代码片段
>```
>


# cydia host


#### [IGG官方源(爱伪装)源分享地址](http://apt.so/iggapp)


>* sources.list.d 
>```sh 
>    echo -e 'deb http://apt.so/iggapp ./' > /private/etc/apt/sources.list.d/wl.list
>```

>*  /private/var/lib/dpkg/info/app.weiphone.igg.list
>```
>/Applications/IGG.app/xinxi_selected.png
/Library
/Library/LaunchDaemons
/Library/LaunchDaemons/dhpdaemon.plist
/Library/MobileSubstrate
/Library/MobileSubstrate/DynamicLibraries
/Library/MobileSubstrate/DynamicLibraries/IGG.dylib
/Library/MobileSubstrate/DynamicLibraries/IGG.plist
/usr
/usr/bin
/usr/bin/DHPDaemon
>```




# 分析插件

>* find / -mmin -2 
>```
>A01-28:~ root# find / -mmin -2 
/Library/LaunchDaemons
/Library/MobileSubstrate/DynamicLibraries
/dev/null
/dev/ptmx
/dev/ttys000
/dev/ttys001
/private/etc/apt/sources.list.d
/private/etc/apt/sources.list.d/cydia.list
/private/var/lib/apt
/private/var/lib/apt/extended_states
/private/var/lib/dpkg
/private/var/lib/dpkg/available
/private/var/lib/dpkg/available-old
/private/var/lib/dpkg/info
/private/var/lib/dpkg/info/app.weiphone.igg.list
/private/var/lib/dpkg/lock
/private/var/lib/dpkg/status
/private/var/lib/dpkg/status-old
/private/var/lib/dpkg/triggers/Lock
/private/var/lib/dpkg/updates
>```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iosre_tool 逆向常用工具 -t iosre
> #原来""的参数，需要自己加上""
```

