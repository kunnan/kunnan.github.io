---
layout: post
title: iosreWIKI
date: 2018-05-28
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: IOS安全学习资料汇总
---


# OSobfuscation





# Check_and_Modify_machO_symbol


>* [检查和修改machO程序的函数符号](https://github.com/iOSobfuscation/Check_and_Modify_machO_symbol)
>* [MachO文件的符号混淆](https://github.com/kunnan/KNCheck_and_Modify_machO_symbol)
>
>* [Dump Kext information from Macos. Support batch analysis. The disassembly framework used is Capstone
](https://github.com/cocoahuke/mackextdump)

```
https://github.com/kunnan/KNmackextdump
```





# RuntimeBrowser

#### [找出没有被引用的方法](https://github.com/kunnan/KN_find_methods_unreferenced)


```sh
devzkndeMacBook-Pro:objc_cover devzkn$ /usr/bin/python objc_cover.py /Users/devzkn/tmp
# the following methods may be unreferenced
+[EJEueKdIvdloplkk _begin]
+[EJEueKdIvdloplkk cleanUp]
+[EJEueKdIvdloplkk hidden]
```

```
alias kncover='/usr/bin/python /Users/devzkn/code/iosre/objc_cover'
devzkndeMacBook-Pro:objc_cover devzkn$ kncover  /Users/devzkn/tmp 
```


#### Graph the import dependancies in an Objective-C project


>* [objc_dep](https://github.com/jbtewaks/objc_dep)
>
>* [Graph the import dependancies in an Objective-C project](https://github.com/kunnan/KNobjc_dep)

```sh
Using options
% python objc_dep.py /path/to/repo -x "(^Internal|secret)" -i subdir1 subdir2 > graph.dot
Will exclude files with names that begin with "Internal", or contain the word "secret". Additionally all files in folders named subdir1 and subdir2 are ignored.
```

#### [CrashReports]( https://github.com/nst/CrashReports)

>* [symbolicatecrash](https://zhangkn.github.io/2018/03/symbolicatecrash/)
>
>




#### KNiOSRuntimeHeadersBrowser

>* [Objective-C Runtime Browser, for Mac OS X and iOS](https://github.com/WxHook/RuntimeBrowser)
>* [KNiOSRuntimeHeadersBrowser](https://github.com/kunnan/KNiOSRuntimeHeadersBrowser)

```
     1. iOS OCRuntime > Frameworks tab > Load All
     2. $ wget -r http://192.168.2.213:10000/tree/
```

# See Also 

>* [Quick Python script over otool to help spotting potentially unused methods in Objective-C Mach-O executable files](https://github.com/iOSobfuscation/objc_cover)
>* [nonworm](http://www.mottoin.com/user/nonworm)
>* [(2).查找无用selector和无用class](https://github.com/nst/objc_cover)
>
>* [pandazheng/IosHackStudy](https://github.com/WxHook/IosHackStudy)
>* [Check_and_Modify_machO_symbol:这个项目用来检查和修改machO程序的函数符号](https://github.com/CocoaHuke/Check_and_Modify_machO_symbol)
>* [[资源帖]分享些资料](http://iosre.com/t/topic/1954)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iosreWIKI IOS安全学习资料汇总 -t iosre
> #原来""的参数，需要自己加上""
```

