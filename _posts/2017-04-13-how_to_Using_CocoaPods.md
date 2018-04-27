---
layout:     post
title:      how_to_Using_CocoaPods
subtitle:   CocoaPods的安装和使用
date:       2017-04-13
author:     
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - iOS
    - Xcode
    - CocoaPods
    - ruby
---

# 前言

 本文的重点在于如何使用，如果对[如何创建pod 比较感兴趣，可看这里](https://kunnan.github.io/2018/04/26/pod_lib_create/)
 
>* [KNPodlib](https://cocoapods.org/pods/KNPodlib)
>
>

#### CocoaPods是iOS最常用最有名的类库管理工具.

>* 解决了两个问题：
```
1、你项目中用到的类库有更新，你必须得重新下载新版本，重新加入到项目。
2、某个类库又用到其他类库，所以要使用它，必须得另外下载其他类库，而其他类库又用到其他类库
```

>* .gitignore 
>```
Pods/
/Pods/
>```

#### Carthage

>* Carthage 
>```
>git "https://github.com/zhuhaow/NEKit" "master"
>github "Mantle/Mantle" ~> 1.5.3
>github "ReactiveCocoa/ReactiveCocoa" ~> 2.4.3
>github "robbiehanson/CocoaAsyncSocket" ~> 7.5.1
>github "CocoaLumberjack/CocoaLumberjack" ~> 3.0.0
>github "lexrus/MMDB-Swift" "0.2.7"
>github "zhuhaow/Sodium-framework" "v1.0.10.1"
>github "behrang/YamlSwift" ~> 3.0.0
>github "zhuhaow/tun2socks" ~> 0.6.0
>github "zhuhaow/Resolver" ~> 0.1.3
>```

>* .gitignore 
>```
> Carthage
Add this line if you want to avoid checking in source code from Carthage dependencies.
Carthage/Checkouts
Carthage/Build
>```

# 正文


#### 安装: 在安装CocoaPods之前，要在本地安装好Ruby环境。

>* insatll
>```
CocoaPods is built with Ruby and is installable with the default Ruby available on OS X. We recommend you use the default ruby.
```

>* gem sources -l
>```
>devzkndeMacBook-Pro:KNPodlib devzkn$ gem sources -l
*** CURRENT SOURCES ***
https://rubygems.org/
>```


#### 升级ruby

	查看ruby版本 
	$ ruby -v
	
	ruby 2.0.0p648 (2015-12-16 revision 53162) [universal.x86_64-darwin16]


使用 **rvm** 管理ruby：安装 ruby，升级ruby
	
	curl -L get.rvm.io | bash -s stable 
	source ~/.bashrc
	source ~/.bash_profile

切换 ruby 源


	$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
	$ gem sources -l
	*** CURRENT SOURCES ***
	
	https://gems.ruby-china.org
	
安装并切换 ruby

> 安装 2.3.3版本，适配其他工具对ruby的版本要求~

	rvm install 2.3.3 --disable-binary
	rvm use 2.3.3 --default
	

有关RVM的使用可以看这篇 [RVM 使用指南](http://qiubaiying.github.io/2017/04/28/RVM-使用指南/)

#### 安装CocoaPods

Using the default Ruby install can require you to use sudo when installing gems.


>* [1.  Further installation instructions are in the guides.](https://guides.cocoapods.org/using/getting-started.html#getting-started)
>```
>sudo gem install -n /usr/local/bin cocoapods
># Xcode 8 + 9
$ sudo gem install cocoapods
>```
>
>* 2. 升级版本库
>```
>		pod setup
>```
	
	这里需要下载版本库（非常庞大），需要等很久
	
		Receiving objects:  72% (865815/1197150), 150.07 MiB | 190.00 KiB/s
	
	或者直接从其他装有cocoapod的电脑中拷贝`~/.cocoapods`到你的用户目录，然后再 `pod setup`会节省不少时间
	
	
>* [We also have a Mac app for CocoaPods. It only gets major releases ATM though.](https://cocoapods.org/app)
>
	
# 使用:get started

#### 创建 `podfile` 文件: Podfile的内容是你想导入的类库


>* CocoaPods provides a `pod init` command to create a Podfile with smart defaults
>```
>devzkndeMacBook-Pro:KNPodlibDemo devzkn$ pod init
devzkndeMacBook-Pro:KNPodlibDemo devzkn$ tree -L 2
.
├── KNPodlibDemo
│   ├── AppDelegate.h
│   ├── AppDelegate.m
│   ├── Assets.xcassets
│   ├── Base.lproj
│   ├── Info.plist
│   ├── ViewController.h
│   ├── ViewController.m
│   └── main.m
├── KNPodlibDemo.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   └── xcuserdata
└── Podfile
```

>*  list the dependencies in a text file named Podfile in your Xcode project directory
>```
>platform :ios, '8.0'
use_frameworks!
target 'MyApp' do
  pod 'AFNetworking', '~> 2.6'
  pod 'SwiftyJSON', '~> 2.3'
end
>```


>* 注意
>```
>Podfile文件应该和你的工程文件.xcodeproj在同一个目录下
>```	

#### install the dependencies in your project：pod install 、pod update  



>* 直接在本地版本库中查找对应的代码库信息，不升级版本库，节省时间
```
	pod install --verbose --no-repo-update
//	若找不到库，再使用下面的命令
	pod install
```
>*  open the Xcode workspace instead of the project file when building your project
>
>```
>Please close any current Xcode sessions and use `KNTestPod.xcworkspace` for this project from now on.
Sending stats
devzkndeMacBook-Pro:KNPodlibDemo devzkn$ open KNPodlibDemo.xcworkspace
>```
>
>
>* Now you can import your dependencies e.g.:
>
>```
>#import <KNPodlib/KNTestWebViewController.h>
>#import <KNPodlib/KNFeedbackViewController.h>
>```

>* podupdate、podinstall的区别
>```
>1) podinstall只会按照Podfile的要求来请求类库，如果类库版本号有变化，那么将获取失败。
>2) 但是 pod update会更新所有的类库，获取最新版本的类库。
>```

>* podupdate的使用场景：下载的网络项目包含了Podfile的时候
>


#### 版本号

对版本号的操作除了指定与不指定(最新版本)，你还可以做其他操作：

-  `\>0.1`  高于0.1的任何版本
- `\>=0.1`  版本0.1和任何更高版本
- `<0.1`  低于0.1的任何版本
- `<=0.1`  版本0.1和任何较低的版本
- `〜>0.1.2`  版本 0.1.2的版本到0.2 ，不包括0.2。

####  Search for pods

>* 判断AFNetworking 是否支持CocoaPods。

```
bogon:~ devzkn$ pod search AFNetworking
Setting up CocoaPods master repo
```


#### Q&A 

>* Unable to run command 'StripNIB HCPCMPayProgress.nib' - this target might include its own product.
>
>```
>1) podfile 打开  use_frameworks! 
>2)再次执行 pod install
>因此此时使用的是frameworks，frameworks静态库自己是包含资源文件（xib）的
>devzkndeMacBook-Pro:KNPodlib devzkn$ tree -L 3
.
├── KNPodlib.bundle
│   ├── Info.plist
│   ├── deleteX.png
│   ├── hebaoGrayPoint.png
│   ├── hebaoWhitePoint.png
│   └── store_add.png
└── KNPodlib.framework
    ├── HCPCMPayProgress.nib
    ├── Headers
    │   ├── HCPEnvrionmentalVariables.h
    │   ├── KNBaseViewController.h
    │   ├── KNBaseWebViewController.h
    │   ├── KNFeedbackViewController.h
    │   ├── KNPodlib-umbrella.h
    │   └── KNTestWebViewController.h
    ├── Info.plist
    ├── KNFeedbackViewController.nib
    ├── KNPodlib
    ├── KNPodlib.bundle
    │   ├── Info.plist
    │   ├── deleteX.png
    │   ├── hebaoGrayPoint.png
    │   ├── hebaoWhitePoint.png
    │   └── store_add.png
    └── Modules
        └── module.modulemap
>```



# 例子

>* 例子
>```
>//# 下面两行是指明依赖库的来源地址
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/Artsy/Specs.git'
//# 说明平台是ios，版本是9.0
platform :ios, '9.0'
//# 忽略引入库的所有警告（强迫症者的福音啊）
inhibit_all_warnings!
//# 针对MyApp target引入AFNetworking
//# 针对MyAppTests target引入OCMock，
target 'MyApp' do 
    pod 'AFNetworking', '~> 3.0' 
    target 'MyAppTests' do
       inherit! :search_paths 
       pod 'OCMock', '~> 2.0.1' 
    end
end
post_install do |installer|       
   installer.pods_project.targets.each do |target| 
     puts target.name 
   end
end
>```
>
>
>* [高级用法 podspec.json](https://github.com/zhangkn/KNMVVMReactiveCocoaDemo/blob/master/Podfile)
>```
>https://github.com/CocoaPods/Specs/blob/master/Specs/7/8/5/OctoKit/0.5/OctoKit.podspec.json
>    pod 'OctoKit', :podspec => 'KNMVVMReactiveCocoaDemo/0.5/OctoKit.podspec.json'
>```



# [crate a pod](https://guides.cocoapods.org/making/getting-setup-with-trunk.html)

>* creating a pod is pretty easy
>```
>$ pod spec create Peanut
$ edit Peanut.podspec
$ pod spec lint Peanut.podspec
>```

>* [本人的教程和例子](https://kunnan.github.io/2018/04/26/pod_lib_create/)
>
>
# see also
>* [pod_lib_create](https://kunnan.github.io/2018/04/26/pod_lib_create/)
>* [如何下载和安装CocoaPods](https://blog.csdn.net/z929118967/article/details/75213888)
>* [KNMVVMReactiveCocoaDemo](https://github.com/zhangkn/KNMVVMReactiveCocoaDemo)
>* [ReactiveCocoa- CSDN博客](https://blog.csdn.net/u011018979/article/details/75220438)
