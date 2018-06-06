---
layout: post
title: iosObfuscation
date: 2018-06-05
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 马甲包混淆方案
---


# 马甲包混淆方案

>* 通过技术手段，多次上架同一款产品的方法.
>* 另一种马甲包是内容和功能完全不合规，壳子本身功能正常是为了能通过审核，而一旦审核通过，大部分都会直接成为一个`UIWebView`展示内容，包括：赌博、色情、以及其他（我司是网赚平台）

# 混淆方案组合一:`Hikari` 混淆调用树



# [混淆方案组合二 混淆方法名，类名](https://zhangkn.github.io/2018/04/iOSobfuscation/)


#### [原版](https://blog.csdn.net/yiyaaixuexi/article/details/29201699)

提供两个文件：func.list、confuse.sh

```
将要混淆的方法名、类名 填入func.list 文件里面。项目里不要引入func.list文件

Build Phases 点击’+’,new run script , Run Script -> add $PROJECT_DIR/confuse.sh

command + B 将生成的 codeObfuscation.h加入项目
```

>* [confuse.sh：简易的混淆脚本，主要思路是把敏感方法名集中写在一个名叫func.list的文件中，逐一#define成随机字符，追加写入.h。------痛点就是一个一个手写](https://gist.github.com/zhangkn/dc2f5e5a883cc506d20fc6285775c313)


# 注意

>使用这套方案，（2种）目的是为了混淆我们的SDK
>```
>1)目前暂未出现因为机审不过的情况，通常是因为马甲本身的功能不够丰富。
>2)另外方案一有可能导致当前项目无法使用xib以及无法继承自定义类的情况，暂时无法解决，发生几率不高，但是还是建议在工程做完之后再修改编译方式。
>```

# 验证效果

#### hopper 

>* `frida-ps -Ua`
>* `kndump appname`
>

#### class-dump


```sh
 swiftOCclass-dump /Users/devzkn/decrypted/kn/Payload/kn.app/kn -H -o  /Users/devzkn/decrypted/kn/head
```

>* [获取头文件](https://github.com/zhangkn/KNBin/blob/master/swiftOCclass-dump)

```
https://github.com/zhangkn/KNBin/blob/master/swiftOCclass-dump

可以class-dump混编的

swiftOCclass-dump  --arch arm64 /Users/devzkn/decrypted/AppStoreV10.2/Payload/AppStore.app/AppStore -H -o  /Users/devzkn/decrypted/AppStoreV10.2/head

swiftOCclass-dump knip  -H -o  /Users/devzkn/decrypted/knip/head
```

#### otool -tv

>*  otool -hv knip

>*  otool -l knip

#### `tweak+ knhook ` 进行跟踪 

```sh
nic 
11 
MobileSubstrate Bundle filter: 可以使用frida-ps -Ua 进行查看
 List of applications to terminate upon installation: 使用 ps -e |grep No
```

>* 修改`Makefile`

```
THEOS_DEVICE_IP=usb2222	#5C9
TARGET = iphone:latest:8.0
ARCHS = armv7 arm64
THEOS=/opt/theos
THEOS_MAKE_PATH=$(THEOS)/makefiles
$(TWEAK_NAME)_CFLAGS += -Wno-error
```
>* 新增 deploy sh

```
echo "" > ~/.ssh/known_hosts
	cd `dirname $0` 
	make clean
	rm -rf ./packages
	make package install
```

>* 添加`knhook`

```makefile
KNHookClass/KNHook.m
```
```xm
#import  "KNHookClass/KNHook.h"
```

```
//寻找UIApplicationDelegate 实例的didFinishLaunchingWithOptions
%hook _AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	// 打印某个类的所有方法的，查看所有方法的执行顺序
		[KNHook hookClass:@""];// 具体的要结合hooper、cycript 进行定位, 
    return %orig;
}
%end
```

```
	%log;
	%orig;
```

#### [cycript 进行快速分析](https://kunnan.github.io/2018/04/20/CycriptTricks/)


```cy
[[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
```


####   使用 `console`进行log 分析


#### process:MobileSafari 远程调试


######  Safari 远程调试
>* 1、首先，我们要打开Safari的远程调试功能

```
Safari的偏好设置  -->高级 --> 在菜单显示“开发”菜单
```

>* 2、打开我们手机，设置-->Safari-->高级-->Web检查器


>* 将iPhone设备连接上Mac，打开Safari的开发菜单，就会发现我们的设备


# 关键词 `jail`

```
subtaskCheckIDFA
download
```


# 熟悉VUE ，了解js


#  destructor、constructor

```
static void __attribute__((destructor)) back_main1(void)
{
printf("Come to %s\n", __func__);
}
```

```
__attribute__((constructor)) static void before1()
{
    printf("beforeFunction\n");
	[KNHook hookClass:@"WebViewJavascriptBridge"];
}
```

# See Also 

>* [code:BQL_iOSProjectMix](https://github.com/iOSobfuscation/BQL_iOSProjectMix)
>

#### [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)


```
WebViewJavascriptBridge 的结构是 WebViewJavascriptBridge(使用 UIWebView) 和 WKWebViewJavascriptBridge(使用 WKWebView) 公用一个 WKWebViewJavascriptBridgeBase,
```

>* [WebViewJavascriptBridge/Example Apps/ExampleApp-iOS/](https://github.com/marcuswestin/WebViewJavascriptBridge/tree/master/Example%20Apps/ExampleApp-iOS)

```
    [_bridge callHandler:@"testJavascriptHandler" data:@{ @"foo":@"before ready" }];
```


#### iOSobfuscation

>* [obfuscator](https://github.com/iOSobfuscation/obfuscator)
>* [Hikari: 基于 obfuscator 进行了Xcode9的适配](https://github.com/iOSHacking/Hikari)
>
>* [马甲包混淆方案:组合 Hikari+ 混淆方法名，类名](http://biqinglin.com/2018/05/06/%E9%A9%AC%E7%94%B2%E5%8C%85%E6%B7%B7%E6%B7%86%E6%96%B9%E6%A1%88/#more)

>* [back zip hikari](https://github.com/iOSobfuscation/KNHikari)

```
1) http://7xunik.com1.z0.glb.clouddn.com/Hikari.zip
```
>*  Uploading LFS objects

```
2) Uploading LFS objects
remote: error: File Hikari.zip is 185.04 MB; this exceeds GitHub's file size limit of 100.00 MB
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.
devzkndeMacBook-Pro:Hikari devzkn$ git lfs track "*.zip" 
Tracking "*.zip"
devzkndeMacBook-Pro:Hikari devzkn$ kngitinit git@github.com:iOSobfuscation/KNHikari.git
Uploading LFS objects:   0% (0/1), 114 MB | 591 KB/s                                                                                                                                                                         
```

#### AppStore上架-技术篇

>* [AppStore上架-技术篇-HTML5过审技术之混淆技巧](https://www.jianshu.com/p/5b53c361514c)

```
1)而是通过创建本地http服务器的形式通过http://localhost:端口的方式浏览
2) 本地服务器那个是用的开源库：github搜索HTTPServer
3) 重点添加classprex前缀和后缀：加入指令混淆和函数封装
4)shell脚本类型和函数的重命名: https://my.oschina.net/FEEDFACF/blog/1627398
```

>*  [iOS壳版本场景下的批量修改类名、属性名、插入混淆代码、修改项目名称的shell脚本](https://gitee.com/dhar/YTTInjectedContentKit)

```
https://gitee.com/xtfjhhsfm/YTTInjectedContentKit.git

https://github.com/iOSobfuscation/KNInjectedContentKit
```

>* [修改类名、属性名、插入混淆代码、修改项目名称的shell脚本](https://github.com/iOSobfuscation/KNInjectedContentKit)


#### 积分墙接口后台

>* [AsoApiAdmin](https://github.com/iOSobfuscation/AsoApiAdmin)
>* [iOS积分墙](https://github.com/iOSHacking/sngyai_server)
>

### aso

>* [IOS端AppStore进行刷榜操作](https://github.com/iosaso/ASO)

>* [http://gantanhao.memotz.com/](http://gantanhao.memotz.com/)


#### 安全

>* [iOS应用程序安全简要总结](http://wufawei.com/2013/11/ios-application-security-summary/)


#### blog
>* [MobDevGroup/iOSResources](https://github.com/MobDevGroup/iOSResources/tree/master/blog)
>* [iOSBlogsCollections](https://github.com/githubPagesio/iOSBlogsCollections)
>
>* [免费的编程中文书籍索引](https://github.com/justjavac/free-programming-books-zh_CN#go)

```
https://github.com/githubPagesio/free-programming-books-zh_CN
```
>* [GitHub国内开发者](http://ghrc.babits.top/)
>* [iOS开发者博客](http://mobdevgroup.com/platform/ios/blog)
>
>* [wiki.jikexueyuan.com](http://wiki.jikexueyuan.com/list/ios/)
>* [iOSBlogCN](https://github.com/cuzv/mark/blob/master/material/iOSBlogCN.md)

###### [wufawei](https://github.com/wufawei)
则平
>* [翻译的关于iOS安全的一系列文章](https://github.com/wufawei/iossecurity)

>* [wufawei.github.com](https://github.com/githubPagesio/wufawei.github.com)


#### 语言学习资料

>* [go](https://github.com/justjavac/free-programming-books-zh_CN#go)

#### other

>* [iOS Jailbroken Programming](https://blog.zz173.com/detail/5)
>* [Sublime Text 3全平台破解思路，源码](https://blog.zz173.com/detail/15)
>* [tryerBreakerNew](https://github.com/iOSHacking/tryerBreakerNew)
>* [reloadNotation.js:应用试客、qianka](https://github.com/Gechmind/tryerBreaker/blob/15d0f12d16594c21a81a074e0083f126dd90ac12/reloadNotation.js)
>* [qianka](https://github.com/iOSobfuscation/python-toolkit/tree/master/qianka)
>* [qianka_task_demo_vue](https://github.com/iOSobfuscation/qianka_task_demo_vue)
>* [FishHook用于hook C函数，是Facebook提供的一个动态修改链接mach-O文件的工具，项目地址：fishhook 。](https://github.com/facebook/fishhook)

```
https://my.oschina.net/FEEDFACF/blog/1528819
https://my.oschina.net/FEEDFACF/blog/1621956
```

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iosObfuscation 马甲包混淆方案 -t iosre
> #原来""的参数，需要自己加上""
```

