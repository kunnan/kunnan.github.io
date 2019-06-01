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

# 前言


#### anti-class-dump

>* [py 加固防dump-OC代码混淆器](https://github.com/iosaso/oc-obfuscator)
>

#### LLVM Obfuscator

>* [`Hikari`](https://github.com/HikariObfuscator/Hikari)

# 马甲包混淆方案

>* 通过技术手段，多次上架同一款产品的方法.
>* 另一种马甲包是内容和功能完全不合规，壳子本身功能正常是为了能通过审核，而一旦审核通过，大部分都会直接成为一个`UIWebView`展示内容

# I、混淆方案组合一:[`Hikari`](https://github.com/HikariObfuscator/Hikari) 混淆调用树



>* 预编译完成的工具链

Hikari的[Releases](https://github.com/HikariObfuscator/Hikari/releases)有预先编译好的工具链，将Hikari.xctoolchain解压到~/Library/Developer/Toolchains/ 或/Library/Developer/Toolchains/ 即可(区别是前者只有当前用户可用，后者所有用户都可使用)

```
/Users/devzkn/code/knHikari/Users/Naville/Library/Developer/Toolchains/Hikari.xctoolchain 

 /Users/devzkn/Library/Developer/Toolchains/Hikari.xctoolchain
```

>* [ macOS Quick Installation: 假设您的macOS中有安装cmake和ninja(`brew install cmake`)](https://naville.gitbooks.io/hikaricn/content/Compile-&-Install.html)

这个脚本假设当前工作目录不是用户的家目录(即~/) ，如果是的话请先cd到其他目录。


```sh
git clone -b release_60 https://github.com/HikariObfuscator/Hikari.git Hikari \
&& mkdir Build && cd Build && \
cmake -G "Ninja" -DCMAKE_BUILD_TYPE=MinSizeRel -DLLVM_APPEND_VC_REV=on -DLLVM_CREATE_XCODE_TOOLCHAIN=on \
-DCMAKE_INSTALL_PREFIX=~/Library/Developer/ ../Hikari && ninja &&ninja install-xcode-toolchain && git clone \
https://github.com/HikariObfuscator/Resources.git ~/Hikari && rsync -ua \ /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/ \ ~/Library/Developer/Toolchains/Hikari.xctoolchain/ && \
rm ~/Library/Developer/Toolchains/Hikari.xctoolchain/ToolchainInfo.plist
```

>* [201806 fork](https://github.com/kunnan/Hikari)



#### 1.1 使用


>* Hikari目前支持下文所述的编译器标记

```
-enable-bcfobf 启用伪控制流  
-enable-cffobf 启用控制流平坦化
-enable-splitobf 启用基本块分割  
-enable-subobf 启用指令替换  
-enable-acdobf 启用反class-dump  
-enable-indibran 启用基于寄存器的相对跳转，配合其他加固可以彻底破坏IDA/Hopper的伪代码(俗称F5)  
-enable-strcry 启用字符串加密  
-enable-funcwra 启用函数封装
```

>* -enable-allobf一次性启用前文所述的所有标记

>* 对于Xcode而言所有的混淆标记应该加在Target的Other C Flags中
>
>  ![image](https://wx3.sinaimg.cn/large/af39b376ly1fug9km577uj20r103u3yt.jpg)

```
以clang为例，您需要在每个flag前加上-mllvm
```




#### 1.2 content/Intergrating-with-Xcode


>* 对于工程中的每个Target.搜索Enable Index-While-Building并设置为NO

```
Build Setting -> Enable Index-While-Building Functionality -> ‘default’ change to ‘No’
```
>* 按照你的混淆需求在CFLAGS中增加混淆标记
>
>  ![image](https://wx3.sinaimg.cn/large/af39b376ly1fug9km577uj20r103u3yt.jpg)

```
开启混淆: 启用控制流平坦化、启用伪控制流, Build Settings -> OTHER_CFLAGS -> -mllvm -enable-cffobf -mllvm -enable-bcfobf
```


>* 记得关闭编译器优化(CFLAG加-O0,Xcode的Optimization Level设置为None,否则混淆会被编译器优化回去)

```
关闭编译优化 Build Settings -> OPTIMIZATION_LEVEL -> 0
```

>* 打开Xcode->Toolchains菜单, 选择 Hikari.
>

```
Build Settings -> Compiler for C/C++/Objective-C -> HikariObfuscator
```
>* [可选]关闭 bitcode

#### 1.3 wiki

>* [wiki](https://legacy.gitbook.com/book/naville/hikaricn/details)
>

#### 1.4 Known Issues

>* Running AntiClassDump On A File Without ObjC Class will crash the executable.在没有ObjC类的源码里打开反class-dump后编译产物会崩溃

#### 1.5 [HikariDemo](https://github.com/iOSobfuscation/KNHikariDemo)


#### 1.6 [code backup](https://github.com/kunnan/KNHikari-20180526)

# II、[混淆方案组合二 混淆方法名，类名](https://zhangkn.github.io/2018/04/iOSobfuscation/)


#### 2.1  [原版](https://blog.csdn.net/yiyaaixuexi/article/details/29201699)

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

# III 、验证效果

#### 3.1  hopper 

>* `frida-ps -Ua`
>* `kndump appname`

![image](https://wx3.sinaimg.cn/large/af39b376gy1fs2bbq43e4j20h20bc3zt.jpg)

#### 3.2 class-dump


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

#### 3.3 otool -tv

>*  otool -hv knip

>*  otool -l knip


#### 3.4  [cycript 进行快速分析](https://kunnan.github.io/2018/04/20/CycriptTricks/)


```cy
[[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
```

#### 3.5 使用 `console`进行log 分析


###### [destructor、constructor & knhook](https://kunnan.github.io/2018/06/05/__attribute__/)

>* [__attribute__](https://kunnan.github.io/2018/06/05/__attribute__/)

```
static void __attribute__((destructor)) back_main1(void)
{
printf("Come to %s\n", __func__);
}
```
>* [knhook](https://kunnan.github.io/2018/06/06/hookClass_knhook_hookClassLog/)

```
__attribute__((constructor)) static void before1()
{
    printf("beforeFunction\n");
	[KNHook hookClass:@"WebViewJavascriptBridge"];
}
```

#### 3.6 process:MobileSafari 远程调试


# IV 、知识补充

#### 还原符号表

#### cmake

>* `/Users/devzkn/Downloads/kevin－software/ios-Reverse_Engineering/llvm-3.9.0.src/build`

>* `/Users/devzkn/code/iosre/Hikari/Hikari/build`


# V、See Also

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

