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


>* gem sources -l
>

**CocoaPods** 是用 ruby 实现的，要想使用它首先需要有 ruby 的环境。


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

1. 安装
	
		sudo gem install -n /usr/local/bin cocoapods
2. 升级版本库

		pod setup
		
	这里需要下载版本库（非常庞大），需要等很久
	
		Receiving objects:  72% (865815/1197150), 150.07 MiB | 190.00 KiB/s
	
	或者直接从其他装有cocoapod的电脑中拷贝`~/.cocoapods`到你的用户目录，然后再 `pod setup`会节省不少时间
	
# 使用

#### 创建 `podfile` 文件: Podfile的内容是你想导入的类库


pod 已经提供了创建 `podfile` 文件的命令，在工程目录下

	pod init

>* 注意
>```
>Podfile文件应该和你的工程文件.xcodeproj在同一个目录下
>```	

#### 加载代码库：pod install 、pod update

>* 直接在本地版本库中查找对应的代码库信息，不升级版本库，节省时间
```
	pod install --verbose --no-repo-update
//	若找不到库，再使用下面的命令
	pod install
```
>* 安装第三方库之后，打开项目文件的方式: xcworkspace
>
>```
>Please close any current Xcode sessions and use `KNTestPod.xcworkspace` for this project from now on.
Sending stats
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

# see also
>* [CocoaPod的使用](https://github.com/zhangkn/KNMVVMReactiveCocoaDemo)
>* [CocoaPods公有仓库的创建](http://qiubaiying.top/2017/03/08/CocoaPods%E5%85%AC%E6%9C%89%E4%BB%93%E5%BA%93%E7%9A%84%E5%88%9B%E5%BB%BA/)
>* [CocoaPods私有仓库的创建](http://qiubaiying.top/2017/03/10/CocoaPods%E7%A7%81%E6%9C%89%E4%BB%93%E5%BA%93%E7%9A%84%E5%88%9B%E5%BB%BA/)

>* [ReactiveCocoa   - CSDN博客](https://blog.csdn.net/u011018979/article/details/75220438)
>* [如何下载和安装CocoaPods](https://blog.csdn.net/z929118967/article/details/75213888)
