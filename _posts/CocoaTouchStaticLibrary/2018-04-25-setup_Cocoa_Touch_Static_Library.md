---
layout: post
title: setup_Cocoa_Touch_Static_Library
date: 2018-04-25
tag: CocoaTouchStaticLibrary
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 搭建一个针对iOS静态库开发的主项目，提高开发效率，方便调试
---


# 前言

很多年前，探索了出了一个提高静态库工程开发调试效率的搭建方法，并在[GitHub创建了对应的开源项目](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)。 

>* [主项目](https://github.com/zhangkn/KNAPP)
>* [子项目：KNCocoaTouchStaticLibrary](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>* [项目的管理方法采用git_subtree](https://kunnan.github.io/2018/04/25/git_subtree/)
>
>



# 搭建静态库的基本步骤


#### 1、创建 app工程 ：KNAPP（主项目）

>* enable bitcode 关闭
>* /Users/devzkn/code/cocoapodDemo/cocoapodStatic/KNAPP
>


#### 2、创建静态库工程： Cocoa Touch Static Library（子项目）


######  配置静态库 target 的build settings :

>* other linker flags
>```
> -ObjC -all_load
>```
>* enable bitcode 关闭
>```
> 关闭 静态库target 的bitcode ;
>关闭 app target的bitcode 
>关闭 bundle target的bitcode
>```
>* [PCH文件](https://github.com/zhangkn/KNCocoaTouchStaticLibrary/blob/master/KNCocoaTouchStaticLibrary/KNCocoaTouchStaticLibrary-Prefix.pch)
>```
>静态库工程的PCH文件和bundle的PCH文件都要设置，且内容要保持一致
>```
>


###### 配置静态库 target 的build phases

>* copy files：配置静态库暴露的头文件
>```
>devzkndeMacBook-Pro:KNCocoaTouchStaticLibrary devzkn$ tree -L 2
.
├── KNCocoaTouchStaticLibrary.h
├── KNFeedbackViewController.h
└── KNTestWebViewController.h
>```
>![](https://ws1.sinaimg.cn/large/006tKfTcgy1fqp2fua6vmj318u0diwfr.jpg)



#### 3、将静态库工程放到app工程(主项目)

>* [将静态库工程放到app工程,采用git_subtree进行管理](https://kunnan.github.io/2018/04/25/git_subtree/)
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ tree -L 2
.
├── KNAPP
│   ├── AppDelegate.h
│   ├── AppDelegate.m
│   ├── Assets.xcassets
│   ├── Base.lproj
│   ├── Info.plist
│   ├── KNAPP.zip
│   ├── ViewController.h
│   ├── ViewController.m
│   └── main.m
├── KNAPP.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   └── xcuserdata
├── KNCocoaTouchStaticLibrary
│   ├── Business
│   ├── KNCocoaTouchStaticLibrary
│   ├── KNCocoaTouchStaticLibrary.xcodeproj
│   ├── KNStaticBundle
│   └── README.md
└── README.md
>```


#### 4、在KNCocoaTouchStaticLibrary静态库工程中创建bundle targets

>* 在KNCocoaTouchStaticLibrary.xcodeproj 中创建bundle工程KNStaticBundle 
>```
>bundle targets 和 静态库KNCocoaTouchStaticLibrary targets并列。 统一在在KNCocoaTouchStaticLibrary.xcodeproj 中管理
>```
>* bundle target的主要用途
>```
>用于存放资源（xib、图片等资源文件）。
>```
>* Bundle 的基本概念
> ```
> 资源文件包。我们将许多图片、XIB、文本文件组织在一起，打包成一个Bundle文件。方便在其他项目中引用包内的资源。
> ```
>
>* Bundle文件的特点
>```
>Bundle是静态的，也就是说，我们包含到包中的资源文件作为一个资源包是不参加项目编译的。也就意味着，bundle包中不能包含可执行的文件（提交appstore 尤其注意这个问题）。它仅仅是作为资源，被解析成为特定的2进制数据。
>```
>
>* 1> 具体的create操作:从MacOS 的framework、library中选择bundle类型进行create 
>```
>细节：
> 创建之后记得将bundle工程的baseSDK 类型修改为iOS SDK，以及supported platforms 为iOS
>```
>* 2> [设置PCH : KNStaticBundle/KNStaticBundle-Prefix.pch](https://github.com/zhangkn/KNCocoaTouchStaticLibrary/blob/master/KNCocoaTouchStaticLibrary/KNCocoaTouchStaticLibrary-Prefix.pch)
>```
> You will also need to set the Prefix Header build setting of one or more of your targets to reference this file.
>```
>
>* 3> 获取bundle target 设置版本的信息
><script src="https://gist.github.com/zhangkn/f4466ed422c4f08d920671531fdb0006.js"></script>
>
>* 配置bundle target 的infoPlist文件路径:KNStaticBundle/KNStaticBundle-Info.plist
>```
>选择KNStaticBundle target -> build settings -> search: info.plist -> info.plist File: KNStaticBundle/Info.plist
>```
>
>* enable bitcode 关闭
>
>* 去掉 bundle target info.plist 的CFBundleExecutable key
>```
>1） 提交appstore 的注意事项: 去掉bundle工程的info plist的CFBundleExecutable key(可执行文件配置信息)
>	<key>CFBundleExecutable</key>
	<string>$(EXECUTABLE_NAME)</string>
>2） 此时如果KNStaticBundle.bundle 中还是包含了可执行KNStaticBundle，记得删除。否则appstore 提交会被拒绝。
>```


#### 5、配置主项目（关键的一步）
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fqp1nnzgc6j31kw0n07ad.jpg)
>* 设置Target dependencies
>```
>选择KNAPP的target -> build phases—–>Target dependencies
>```
>* Link Binary With Libraries
>```
>选择KNAPP的target -> build phases—–>link binary with libraries—–>+ 选择要添加的库
>```
>* copy Bundle Resources
>```
>直接从静态库工程的Products目录，直接拖拽KNStaticBundle.bundle 到copy Bundle Resources 区域 
>```
>* 配置search path：静态库暴露出来的头文件所在的路径,否则无法找到静态库的文件
>![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqp1vj1xmlj31jy040dgp.jpg)
>```
>├── KNCocoaTouchStaticLibrary
│   ├── Business
根据目前主项目、子项目的关系对应的search路径为：./静态库的工程名/Business/PublicInterface----暴露给外围调用的头文件目录
>这个路径的目录最好都采用实体目录,便于管理。
>```
>
#### 6、灵活使用静态库和主项目

>*  主项目可以作为一个独立的app 上传app store
>* 子项目KNCocoaTouchStaticLibrary，可以提供给第三方集成；
>```
>可以采用CocoaPods的公有、私有仓库
>```
>* 编译KNAPP工程之后，将.a 、include头文件以及bundle文件 提供给第三方集成
>```
>├── KNStaticBundle.bundle
│   ├── HCPCMPayProgress.nib
│   ├── IMG_3604.JPG
│   ├── Info.plist
│   ├── KNFeedbackViewController.nib
│   ├── KNStaticBundle
│   ├── deleteX.png
│   ├── hebaoGrayPoint.png
│   ├── hebaoWhitePoint.png
│   └── store_add.png
├── include
│   └── KNCocoaTouchStaticLibrary
│       ├── KNCocoaTouchStaticLibrary.h
│       ├── KNFeedbackViewController.h
│       └── KNTestWebViewController.h
└── libKNCocoaTouchStaticLibrary.a
// 此时记得去掉KNStaticBundle.bundle/KNStaticBundle
>```
>

# 基础知识


#### [about xcode](https://kunnan.github.io/2016/12/07/about_xcode/)

>* appicon 的所有尺寸
>```
>40*40 60*60 58*58 87*87 80*80 120*120 180*180
>```
>* 伪https 证书的安装（安装cer 证书教程）
>```
>如果测试环境的的https链接不是有效证书的时候，需要在测试安装对应的证书。
>1) 证书可通过iPhone自带的邮件安装
>```

####  Header Search Paths

没有被add到项目里的头文件，可以通过配置Header Search Paths 来引入头文件，这样的好处可以不让project 包含的文件太多，便于管理。 

>* 1） Header Search Paths 和 User Header Search Paths 的区别 
>```
> #include 引入头文件的方式有两种 <> 和 “”
> 1) <> 是只从 Header Search Paths 中搜索
> 2) “” 则能从 Header Search Paths 和 User Header Search Paths 中搜索
> 3) 例子：
> $(SRCROOT)/Libs/ASIHttprequest/ASIHTTPRequestStaticLib/Classes
> $(PROJECT_DIR)/Libs/Wechat
> 4）User Header Search Paths 可配置一些本地第三方开源库：SDWebImage 这样可以避免将这类第三方库加到静态库工程中，导致与集成端的类名冲突。
>```
>

#### 关于集成第三方静态库导致类名冲突的解决方法

有时是静态库和集成端的分类冲突，大部分是共同使用了同一种第三方框架导致的
>* 方式一： 告诉集成端，静态库使用的第三方框架的版本信息
>* 推荐的解决方式： 静态库添加第三方库的时候，不进行add to targets
>```
>这种方式，是最理想的
>例子静态库中使用的AFN框架没有添加到静态库工程的targets，这样能达到编译不报错的目的；  
>这样就要求使用.a 文件的测试app工程需要将对应的AFN框架文件添加到targets，这样就避免了文件冲突。
>```
>

#### 分类的处理

>* 第三方库里对系统库的类加了 category
>```
>需要使用编译参数： -ObjC ，这样第三方库中对系统类作的扩展方法才能在工程中使用;因此，自己制作一个库（静态库）的话，要注意两点：
>1） 避免对系统类加 category
>2） 如果库中用到了其它的第三方的源代码，尤其是用的比较普遍的，如 Reachability, 一定一定要对
这些类重命名，最常见的作法就是给类名加个前缀。以避免别人用你的库时，产生 duplicate symbol 的问题
>```
>
>* -all_load、-ObjC、-force_load
>```
>1)-all_load: 
>Loads all members of static archive libraries.
>2) -ObjC
> Loads all members of static archive libraries that implement an Objective-C class or category.
> 3)-force_load (path_to_archive)
>  Loads all members of the specified static archive library. 
> Note: -all_load forces all members of all archives to be loaded. This option allows you to target a specific archive.
>```
>```
>翻译:
>-all_load:加载静态库文件中的所有成员，
>-ObjC:加载静态库文件中实现一个类或者分类的所有成员，
>-force_load（包的路径）:加载指定路径的静态库文件中的所有成员。所以对于使用runtime时候的反射调用的方法应该使用这三个中的一个进行link，以保证所有的类都可以加载到内存中供程序动态调用
>```
>
>* ps: 图片格式--小知识
>```
>png图片如果用了@2x 、@3x会自动转换成tiff格式的图片。设置不转换的方法是 在bundle的target中 Build Settings 里的 COMBINE_HIDPI_IMAGES 设置为NO
>```
>
#### 减少静态库的大小

>* 去掉静态库工程的调试符号--Generate Debug Symbols:No
>```
>:调试符号可以帮助你调试程序，可以追踪到编译器提供的库和操作系统本身的代码
>```


#### 关于静态库和动态库的知识补充：

>* 库是程序代码的集合，是共享程序代码的一种方式
>```
>根据源代码的公开情况，库可以分为2种类型：
>1) 开源库（公开源代码，能看到具体实现，比如SDWebImage、AFNetworking）；
>2)闭源库（不公开源代码，是经过编译后的二进制文件，看不到具体实现；主要分为：静态库、动态库） 
>```
>
>* [我个人在正向开发其实，经常接触的是.a的静态库](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>* [在我做逆向开发期间，就经常使用theos，创建.dylib的动态库](https://github.com/zhangkn/KNAlipayWalletTweakDemo)
>
>
>* 静态库：.a 和 .framework
>* 动态库：.dylib 和 .framework
>
>* 静态库和动态库在使用上的区别
>```
>：1) 链接时，静态库会被完整地复制到可执行文件中，被多次使用就有多份冗余拷贝
>: 2)动态库：链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存（项目中如果使用了自制的动态库，不能被上传到AppStore）
>```
###### 静态库
>* 制作静态库的注意点 
>```
>无论是 .a 静态库还是 .framework 静态库，最终需要的都是：二进制文件 + .h + 其它资源文件 
>```

>* a 和 .framework 的使用区别
>```
>1) .a 本身是一个二进制文件，需要配上 .h 和 其它资源文件 才能使用；
>2).framework 本身已经包含了 .h 和 其它资源文件，可以直接使用 
>```
>
>* 多文件处理
>```
>如果静态库需要暴露出来的 .h 比较多，可以考虑创建一个主头文件
>```
>
>* .framework为什么既是静态库又是动态库?
>```
>系统的 .framework 是动态库，我们自己建立的 .framework 是静态库
>```
>
>* 静态库中包含了Category
>```
>1)如果静态库中包含了Category，有时候在使用静态库的工程中会报“方法找不到”的错误（unrecognized selector sent to instance）
>2) 解决方案：在使用静态库的工程中配置Other Linker Flags为-ObjC 
>```
>
>* 合并真机和模拟器的.a文件 :让一个.a文件能同时用在真机和模拟器上
>```
>在终端输入指令：
>lipo -create Debug-iphoneos/libMJRefresh.a Debug-iphonesimulator/libMJRefresh.a -output libMJRefresh.a 
>```
>* 通过lipo –info libMJRefresh.a可以查看 .a 的类型（模拟器还是真机）
><script src="https://gist.github.com/zhangkn/12c091a2c57ee6e016e9330e2cc78bb0.js"></script>

#### Xcode7之后 添加PCH文件的方法

>* 在Supporting Files目录下,选择 File > New > File > iOS > Other > PCH File
>
>* 给你的PCH文件起名字TestDemo-Prefix.pch
>
>* Project > Build Settings > 搜索 “Prefix Header“
>```
> 1)YourProjectName/YourProject-Prefix.pch (如 TestDemo/TestDemo-Prefix.pch )
> 2) 将Precompile Prefix Header为YES，预编译后的pch文件会被缓存起来，可以提高编译速度
>```
>* 
>
>
>

# 开发静态库的经验之谈

##### 退出静态库的时候，选择POP还是present?

>* 退出静态库的时候，选择POP还是present
>```
>本人建议全部采用present。因为present 方式具体独立性，将自己的静态库与集成静态库的主端隔离。
>```
>* 如果自己的控制器需要展示主端唤起静态库时的页面当中背景，可以采用代码截图当背景即可。
<script src="https://gist.github.com/zhangkn/b86a4429363211913d04de7b71171123.js"></script>

>* iOS判断当前ViewController是push还是present方式显示的
><script src="https://gist.github.com/zhangkn/1f3081414ab29b6a6a94962f47db5b62.js"></script>

# Q&A 

####  Q1、Unknown class MyClass in Interface Builder file.

>* 原因
>```
由于静态框架采用静态链接，linker会剔除所有它认为无用的代码。不幸的是，linker不会检查xib文件，因此如果类是在xib中引用，而没有在O-C代码中引用，linker将从最终的可执行文件中删除类
```

>* [例子: HCPCMPayProgress.xib](https://github.com/zhangkn/KNCocoaTouchStaticLibrary/blob/master/Business/HCPCMPayProgress.xib)
>

###### 解决方式一：在框架的另一个类中加一个该类的代码引用

>* [解决方式一的例子：推荐](https://github.com/zhangkn/KNCocoaTouchStaticLibrary/blob/master/Business/HCPPopLoadingDialog.m)
>
>```
>+ (void)initialize{
    [HCPCMPayProgress class];
}
//避免在使用你的框架时关闭linker优化（关闭linker优化会导致object文件膨胀）
>```

###### 方式二： 让框架的最终用户关闭linker的优化选项

>* 让框架的最终用户关闭linker的优化选项
>```
>让框架的最终用户关闭linker的优化选项，通过在他们的项目的Other Linker Flags中添加-ObjC和-all_load。
>```

>* ps
```
苹果内置框架不会发生这个问题，因为他们是运行时动态加载的，存在于iOS设备固件中的动态库是不可能被删除的。
```

#### Q2、 ld: -bundle and -bitcode_bundle (Xcode setting ENABLE_BITCODE=YES) cannot be used together

>* [将bundle的release和debug的bitcode 进行关闭即可](http://stackoverflow.com/questions/34770802/ld-bundle-and-bitcode-bundle-xcode-setting-enable-bitcode-yes-cannot-be-use)
>
>* 编译Debug、Release 版本的时候，注意静态库工程、boundle 与集成方的设置保持一致;尤其是bitcode
>
>* ps：去掉 bundle target info.plist 的CFBundleExecutable key
>```
>1） 提交appstore 的注意事项: 去掉bundle工程的info plist的CFBundleExecutable key(可执行文件配置信息)
>	<key>CFBundleExecutable</key>
	<string>$(EXECUTABLE_NAME)</string>
>2） 此时如果KNStaticBundle.bundle 中还是包含了可执行KNStaticBundle，记得删除。否则appstore 提交会被拒绝。
>```

# see also 

>* [桥接](http://blog.csdn.net/z929118967/article/details/74331776)

>* [搭建一个提高开发效率的iOS静态库工程](https://blog.csdn.net/z929118967/article/details/73872024)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost setup_Cocoa_Touch_Static_Library 搭建一个针对iOS静态库开发的主项目，提高开发效率，方便调试 -t CocoaTouchStaticLibrary
> #原来""的参数，需要自己加上""
```

