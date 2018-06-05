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



# GitHub 资源

>* [studyFiles](https://github.com/kunnan/studyFiles)

# OSobfuscation

>* [obfuscator](https://github.com/iOSobfuscation/obfuscator)
>* [Hikari: 基于 obfuscator 进行了Xcode9的适配](https://github.com/iOSHacking/Hikari)
>
>* [马甲包混淆方案:组合 Hikari+ 混淆方法名，类名](http://biqinglin.com/2018/05/06/%E9%A9%AC%E7%94%B2%E5%8C%85%E6%B7%B7%E6%B7%86%E6%96%B9%E6%A1%88/#more)

>* [back zip hikari](https://github.com/iOSobfuscation/KNHikari)

```
http://7xunik.com1.z0.glb.clouddn.com/Hikari.zip
```


#### iOS-private-api-scanner

>* [iOS-private-api-scanner](https://github.com/WxHook/iOS-private-api-scanner)
>


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


# tool

>* [一个包含iPA重签名以及dylib注入的命令行工具](https://github.com/jbtewaks/ktool)
>

# aso\积分墙

#### [应用试客（iTry.com）](https://shike.com/)



#### 钱咖简介

钱咖是一家纯移动互联网公司，是一款通过手机试玩应用免费赚钱的应用，目前支持苹果与安卓版（通过手机浏览器访问qianka.com下载），目前月流水超千万、iOS用户超2000万、微信服务号关注超600万。

>* [e.qianka.com： 客户](https://e.qianka.com/)


>* [www.qianka.com: 用户](https://www.qianka.com/)

```
做任务不能关闭app： 
绑定你的账号和设备
```

>* safari : 抢任务-》获取关键词，app（具体的任务页面）-》领取任务、奖励

```
//具体的任务页面： 检测助手是否打开
1) 前往app store
2）开始试完，打开《宝宝亲》app，宝宝亲会直接打开对应的”任务app“
-- 这个过程有通信流程
3） 领取奖励-〉 判断时间，继续试完-》 完成任务
4） 取消任务
```

>* 证书安装

```
安装证书： 确保设备唯一性： push,获取设备ID，设备注册质询：加密的描述文件服务
签署证书： 
```

>* 绑定手机号码否则，无法保存收益

>* [高级逆向工程师 30K-40K](https://www.zhipin.com/job_detail/3632668511370b790XBz3d25Fw~~.html?ka=comp_joblist_2_blank&lid=291fca63-7f02-41d4-ab53-10636b9c0f6c.brand_jod_list)
>

#### see also
>* [检测用户是否在App Store下载 是否越狱 是否第一次下载 是否删除后下载 如果是当前手机 当前的账号都是第一次下载 则发送对应的奖励](https://github.com/maxfong/MFSIdentifier/issues/2)

```
1) 是否在App Store下载:一般是应用内添加refid标识对应渠道

你自己定义的，比如App Store为10000，PP助手为10001等等，iOS应用 大部分都是App Store下载，小渠道或者越狱取到的量很少的

2)是否越狱:这个能查得到
3)是否第一次下载:现在KeyChain可用，所以查询存储在KeyChain的数据，存在则表示不是第一次安装，集合iCloud刷机也能判断
4)是否删除后下载:添加校验逻辑，给应用累加一个标识符，再根据是否第一次下载判断是否重新安装
5)如果是当前手机：可以根据Telephony、屏幕分辨率、以及设备名称等，再根据存储的标识符做校验
6)当前的账号都是第一次下载:以上问题都解决，这个也就知道了
7)KeyChain不可用时，可用Safari Cookie、iCloud替代
ps:很多这类应用会有一个企业助手，可调用的私有API的
```
>* [苹果赚钱网](http://www.shouzuanapp.com/)
>* [ios](http://www.shouzhuanapp.com/app/ios/)
>* [从技术角度聊聊ASO](http://news.deepaso.com/aso/aso-fromtech.html)
>* [iOS App获取唯一标识符方案](https://github.com/maxfong/MFSIdentifier)


# See Also 
>* [iphonedevwiki.net](http://iphonedevwiki.net/index.php/Main_Page)
>* [ios-security-defense](http://wiki.jikexueyuan.com/project/ios-security-defense/)
>* [iOS 逆向工程资料整理](https://niyaoyao.github.io/2017/05/09/Learning-Reverse-From-Today-D4/)
>* [移动App入侵与逆向破解技术－iOS篇](https://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653577384&idx=1&sn=b44a9c9651bf09c5bea7e0337031c53c&scene=0#wechat_redirect)
>* [iOS安全些许经验和学习笔记](https://bbs.pediy.com/thread-209014.htm)
>* [blog &BBS For iosre](https://segmentfault.com/a/1190000011790846)
>* [iOS-private-api-checker](https://github.com/NetEaseGame/iOS-private-api-checker)
>* [ming1016](https://github.com/ming1016/study/wiki)
>* [学习资料资源入口整理（一起整理啦）](http://iosre.com/t/topic/4680)

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

