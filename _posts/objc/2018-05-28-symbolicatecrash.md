---
layout: post
title: symbolicatecrash
date: 2018-05-28
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 符号化
---



# 前言

> * symbolicatecrash 
>
>   ```
>   ➜  ~ symbolicatecrash --dsym=/Users/devzkn/Library/Developer/Xcode/DerivedData/wxControlTweak-dyfansxvurxnxgafqnbogimgkqpb/Build/Products/Debug-iphoneos/wxControlTweak.dylib.dSYM LatestCrashkn.ips | open -f
>   ```
>
>   

>* 每次编译后生成的 dSYM 文件

```
dSYM 文件里存储了函数地址映射，这样调用栈里的地址可以通过 dSYM 这个映射表能够获得具体函数的位置。一般都会用来处理 crash 时获取到的调用栈 .crash 文件将其符号化

<!--  1、 通过 Xcode 进行符号化-->

将 .crash 文件，.dSYM 和 .app 文件放到同一个目录下，打开 Xcode 的 Window 菜单下的 organizer，再点击 Device tab，最后选中左边的 Device Logs。选择 import 将 .crash 文件导入就可以看到 crash 的详细 log 了。


<!-- 2、通过命令行工具 symbolicatecrash 来手动符号化 crash log -->
本文的重点
export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
symbolicatecrash appName.crash appName.app > appName.log


```

>* 查找symbolicatecrash 工具的目录

```
在Xcode目录下查找/Applications/Xcode.app/Contents
find . -name  'symbolicatecrash' -ls
/Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash

<!-- 便于以后的使用，最好采用ln ，这样避免拷贝，尤其是后台的工程师尤其注意这点-->
devzkndeMacBook-Pro:LatestBuild devzkn$ ln -s /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash ~/bin

devzkndeMacBook-Pro:LatestBuild devzkn$ ls -lrt ~/bin
lrwxr-xr-x  1 devzkn  staff     111 Mar 12 17:28 symbolicatecrash -> /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash
```

# 正文


>* symbolicatecrash

```
Error: "DEVELOPER_DIR" is not defined at /Users/devzkn/bin/symbolicatecrash line 69.
```
>* devzkndeMacBook-Pro:Contents devzkn$ export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
```
<!-- 想要永久生效，就配置到配置文件 -->
devzkndeMacBook-Pro:~ devzkn$ open -e  .bash_profile
export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
source ~/.bash_profile
```

>* 符号化
```
devzkndeMacBook-Pro:LatestBuild devzkn$ /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash --dsym=/Users/devzkn/work/.git//LatestBuild/.dylib.dSYM /Users/devzkn/Desktop/SpringBoard-2018-03-12-162524.ips  --output=/Users/devzkn/Desktop/kn.crash
```

>* 效果

```
Exception Type:  EXC_BAD_ACCESS (SIGSEGV)
Exception Subtype: KERN_INVALID_ADDRESS at 0x0000000000000010
Triggered by Thread:  16

Thread 16 name:  com..
Thread 16 Crashed:
0   .dylib              	0x0000000109b639b8 0x109b54000 + 63928
1   .dylib              	0x0000000109b639a0 0x109b54000 + 63904
2   .dylib              	0x0000000109b63330 0x109b54000 + 62256
3   .dylib              	0x0000000109b631f0 0x109b54000 + 61936
4   .dylib              	0x0000000109b8084c 0x109b54000 + 182348
5   .dylib              	0x0000000109b812b4 0x109b54000 + 185012
6                       	0x0000000188225048 0x18811b000 + 1089608

<!-- 符号化之后的结果 -->
Thread 16 Crashed:
0   .dylib              	0x0000000109b639b8 +[ASWindowKNTool setupSwitchIpABU1YUN1:] (ASWindowKNTool.m:289)
1   .dylib              	0x0000000109b639a0 +[ASWindowKNTool setupSwitchIpABU1YUN1:] (ASWindowKNTool.m:287)
2   .dylib              	0x0000000109b63330 +[ASWindowKNTool setupSwitchIp:] (ASWindowKNTool.m:88)
3   .dylib              	0x0000000109b631f0 +[ASWindowKNTool setupCloseOpenWifi] (ASWindowKNTool.m:62)
4   .dylib              	0x0000000109b8084c -[ ] (.m:501)
5   .dylib              	0x0000000109b812b4 -[ ] (.m:644)
6                       	0x0000000188225048 __NSThreadPerformPerform + 340


```

#  find  CrashReporter file 


>* find . -name  'SpringBoard*' -ls
```
12598152   92 -rw-rw-rw-   1 mobile   mobile      94165 Mar 12 16:25 ./private/var/mobile/Library/Logs/CrashReporter/SpringBoard-2018-03-12-162524.ips
```

>* /private/var/mobile/Library/Logs/CrashReporter
```
-rw-rw-rw- 1 mobile mobile 373035 Mar 12 16:09 panic-2018-03-12-160904.ips
-rw-rw-rw- 1 mobile mobile  93633 Mar 12 16:17 SpringBoard-2018-03-12-161753.ips
-rw-rw-rw- 1 mobile mobile  94099 Mar 12 16:18 SpringBoard-2018-03-12-161853.ips
-rw-rw-rw- 1 mobile mobile  93633 Mar 12 16:20 SpringBoard-2018-03-12-162025.ips
-rw-rw-rw- 1 mobile mobile  94015 Mar 12 16:21 SpringBoard-2018-03-12-162151.ips
-rw-rw-rw- 1 mobile mobile  94165 Mar 12 16:25 SpringBoard-2018-03-12-162524.ips
```

>* 简单的例子

```

devzkndeMacBook-Pro:com.wl..git devzkn$ scp usb2222:/private/var/mobile/Library/Logs/CrashReporter/SpringBoard-2018-03-23-153316.ips ~


devzkndeMacBook-Pro:com.wl..git devzkn$ symbolicatecrash --dsym=/Users/devzkn/com..dylib.dSYM /Users/devzkn/SpringBoard-2018-03-23-153316.ips | open -f

```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost symbolicatecrash 符号化 -t objc
> #原来""的参数，需要自己加上""
```
