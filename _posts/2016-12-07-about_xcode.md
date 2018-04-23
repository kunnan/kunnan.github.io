---
layout:     post
title:      about_Xcode
subtitle:   Xcode 的一些基本操作
date:       2016-12-07
author:     kunnan
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - iOS
    - Xcode
---


# xcode 的调试功能

####  Debug Memory Grap

点击 Debug Memory Graph 按钮。


# xcworkspace

>* 修改工作空间包含的工程内容 xcworkspace/contents.xcworkspacedata

```
<FileRef
location = "group:name/name.xcodeproj">
</FileRef>
```

>* 查看当前Xcode版本

```
gcc --version
```

>* 设置默认的Xcode 

```
#sudo xcode-select -switch /Applications/Xcode.app
 sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
 sudo xcodebuild -license

```

# NSLog

>* 查看iOS10设备的日志，越来越喜欢使用mac的 Console 程序

>* ios8、9 的日志查看可以使用tail -f 直接查看log 文件，或者使用socat

```
socat - UNIX-CONNECT:/var/run/lockdown/syslog.sock
```


# 忽略 Xcode 8 中的注释警告

#### 原因

从Xcode8.0开始，引入了文档注释警告，虽然是件好事，可是各种三方库爆出了一大堆警告.

#### 解决方法：Bulid Settings -> Documentation Comments -> NO

`Bulid Settings` -> `Documentation Comments` -> **`NO`**


# 打包成ipa

>* xcrun

```
xcrun -sdk iphoneos PackageApplication -v Wat.app -o ~/Wat.ipa
```

>* dylib进行签名

```
codesign -f -s "iPhone Developer:xxx" xxx.dylib
```

# plutil 查看plist文件


# 工具类

#### [NSDictionary (Log)、NSArray(Log)](https://github.com/zhangkn/IOSStudy/blob/master/HWeibo/Foundation%2BLog.m)
```
通过重写descriptionWithLocale: ,实现在 Xcode 控制台输出中文的方法。
```
# see also

- [Xcode](https://zhangkn.github.io/2018/01/Xcode/)

```

<!-- dpkg-deb -->

1) Layout/debian/postinst: 对应目录文件/var/lib/dpkg/info/**postinst

2) iPhone:/var/lib/dpkg/info root# ls -lrt  *postinst

3) dpkg-deb -Zgzip -b ~/work/akno.git/Package ~/work/akno.git/com.akno.akno.deb

4) 解压deb包控制文件和常规文件:  dpkg-deb -R com.aun.ios_0.0.1-95+debug_iphoneos-arm.deb ./ayu

5)  dpkg-deb -c 查看内容文件

6）# dpkg -l 查询所有安装的包

7） 卸载： ssh usb2222 dpkg -r com.ios 

8）codesign： codesign -f -s "iPhone Developer:xxx” —entitlements Entitlements.plist Wat.app

9）ldid命令查看签入的内容： devzkndeMacBook-Pro:bin devzkn$ ldid -e /Users/devzkn/decrypted/AppStoreV10.2/Payload/AppStore.app/AppStore 
```

>* [iOSOpenDevInstallFailed](https://zhangkn.github.io/2018/01/iOSOpenDevInstallFailed/)

```
#  rm -rf ZXPUnicodeDecodePlugsForXcode.xcplugin 删除第三方插件
cd ~/Library/Application*Support/Developer/Shared/Xcode/Plug-ins/

# 方式二，或者直接查询DVTPlugInCompatibilityUUID；对应的插件找到info.plist,
找到DVTPlugInCompatibilityUUIDs，新增一项UUID

devzkndeMacBook-Pro:taoke devzkn$ defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID
```

- [usefulCommand](https://zhangkn.github.io/2017/12/usefulCommand/)


