---
layout: post
title: pod_lib_create
date: 2018-04-26
tag: CocoaTouchStaticLibrary
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: cocoaPods开发打包静态库,比我之前自己搭建的模板更方便 
---


# 前言

之前我自己搭建了一个模板，采用[git_subtree](https://kunnan.github.io/2018/04/25/git_subtree/)进行管理；这个喜欢上了使用cocoaPods开发打包静态库.

>* [以前自己搭建的静态库模板](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>```
>开发一个用git_subtree管理依赖关系的静态类库给其他人使用，打包成.a文件。
>```
>* 今天的重点就是换成使用pod管理依赖关系的静态类库
>
>* pwd
>```
>/Users/devzkn/code/cocoapodDemo/cocoapodStatic
>```

# CocoaTouchStaticLibrary

#### 创建开发静态库的方式

>* [不基于pod手动创建(deprecated),我使用了很长的一段时间，自己探索了一套模板](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>
>```
> Xcode->Cocoa Touch Static Library
> [可选]:创建Podfile文件、执行pod install完成整个项目的搭建
> [可选]：demo使用pod添加对私有静态库的依赖
>```
>
>* 基于pod自动创建`pod lib create`
>


#  pod lib --help


####  Develop pods

>* Commands:
>
>```
>    + create    Creates a new Pod
>     + lint      Validates a Pod
>```
>

#### [Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

>* Getting started
>```
>pod lib create KNPodlib
>1) Note: To use your own pod-template you can add the parameter --template-url=URL where URL is the git repo containing a compatible template.
>2)Second Note: you can press return to select the default (underlined) option.
>```
>* Objective-C or Swift
>```
>For both choices CocoaPods will set up your library as a framework.
>```
>
>* Making a Demo Application
>
>```
> This means you don't have to go through creating a new project in Xcode.
> If you want to have an example project for pod try MyLib or need to have your library's tests run inside an application ( interaction tests, custom fonts, etc ) then you should say yes.
>  A good metric is "Should this Pod include a screenshot?"; if so, then you should have a demo.
>```
>
>* Choosing a Test Framework
>
>```
>1) in Objective-C we include a choice of two popular testing frameworks; Specta/Expecta and Kiwi. If you can't decide, use Specta/Expecta.
>We include all the necessary includes and setup for your testing framework in MyLib-Tests.pch so that you don't have to include them in each file.
>2) In Swift we only offer the choice of Quick/Nimble as this looks to be the dominant testing library.
>```
>
>* [Specta/Expecta](https://github.com/specta/specta)
>```
>A light-weight TDD / BDD framework for Objective-C & Cocoa.
>```
>
>* [Kiwi](https://github.com/kiwi-bdd/Kiwi)
>```
> a Behaviour Driven Development library for iOS development. The goal is to provide a BDD library that is exquisitely simple to setup and use.
>```
>* View-based Testing
>
>```
>We recommend using FBSnapShotTestCase, if you are using Specta/Expecta then we include a Pod to improve the syntax.
>```
>
>* Prefixes for Objective-C
>```
>We know that Apple is deprecating prefixes, but in reality they still have their place in Objective-C codebases.
>```

#### The Pod Lib Create Template


>* The Pod Lib Create Template :tree -L 2
>```
> run pod install on the newly created Project
> .
├── Example
│   ├── KNPodlib
│   ├── KNPodlib.xcodeproj
│   ├── KNPodlib.xcworkspace
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   └── Tests
├── KNPodlib
│   ├── Assets
│   └── Classes
├── KNPodlib.podspec
├── LICENSE
├── README.md
└── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj
10 directories, 5 files
>```




#### 实践

>
>* pod lib create KNPodlib
>```
>Cloning `https://github.com/CocoaPods/pod-template.git` into `KNPodlib`.
Configuring KNPodlib template.
To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
To learn more about creating a new pod, see `http://guides.cocoapods.org/making/making-a-cocoapod`.
>```
>
>* To get you started we need to ask a few questions, this should only take a minute.
>
>```
>The domain/default pair of (org.cocoapods.pod-template, HasRunbefore) does not exist
If this is your first time we recommend running through with the guide: 
 - https://guides.cocoapods.org/making/using-pod-lib-create.html
 ( hold cmd and double click links to open in a browser. )
>```
>*  ask a few questions
>```
>What platform do you want to use?? [ iOS / macOS ]
 > ios
What language do you want to use?? [ Swift / ObjC ]
 > ObjC
Would you like to include a demo application with your library? [ Yes / No ]
 > yes
Which testing frameworks will you use? [ Specta / Kiwi / None ]
 > Specta
Would you like to do view based testing? [ Yes / No ]
 > Yes
What is your class prefix?
 > KN
Running pod install on your new library.
Analyzing dependencies
Fetching podspec for `KNPodlib` from `../`
Downloading dependencies
Installing Expecta (1.0.6)
Installing Expecta+Snapshots (3.1.1)
Installing FBSnapshotTestCase (2.1.4)
Installing KNPodlib (0.1.0)
Installing Specta (1.0.7)
Generating Pods project
Integrating client project
[!] Please close any current Xcode sessions and use `KNPodlib.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There are 5 dependencies from the Podfile and 5 total pods installed.
[!] Automatically assigning platform `ios` with version `9.3` on target `KNPodlib_Example` because no platform was specified. Please specify a platform for this target in your Podfile. See `https://guides.cocoapods.org/syntax/podfile.html#platform`.
 Ace! you're ready to go!
 We will start you off by opening your project in Xcode
  open 'KNPodlib/Example/KNPodlib.xcworkspace'
To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
To learn more about creating a new pod, see `http://guides.cocoapods.org/making/making-a-cocoapod`.
>```
>
>
>

# Q&A

#### Q1、cannot load such file -- xcodeproj (LoadError)

>* pod lib create KNPodlib
>```
>devzkndeMacBook-Pro:cocoapodStatic devzkn$ pod lib create KNPodlib
Cloning `https://github.com/CocoaPods/pod-template.git` into `KNPodlib`.
Configuring KNPodlib template.
Traceback (most recent call last):
	6: from ./configure:4:in `<main>'
	5: from ./configure:4:in `each'
	4: from ./configure:5:in `block in <main>'
	3: from ./configure:5:in `require_relative'
	2: from /Users/devzkn/code/cocoapodDemo/cocoapodStatic/KNPodlib/setup/ProjectManipulator.rb:1:in `<top (required)>'
	1: from /usr/local/Cellar/ruby/2.5.1/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require'
/usr/local/Cellar/ruby/2.5.1/lib/ruby/2.5.0/rubygems/core_ext/kernel_require.rb:59:in `require': cannot load such file -- xcodeproj (LoadError)
To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
To learn more about creating a new pod, see `http://guides.cocoapods.org/making/making-a-cocoapod`.
>```
>
>* ruby -v、pod  --version
>```
>devzkndeMacBook-Pro:rubygems devzkn$ ruby -v   
ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-darwin17]
devzkndeMacBook-Pro:rubygems devzkn$ pod  --version
1.4.0
>```
>
>* A1：gem install xcodeproj
>```
>Fetching: atomos-0.1.2.gem (100%)
Successfully installed atomos-0.1.2
Fetching: CFPropertyList-3.0.0.gem (100%)
Successfully installed CFPropertyList-3.0.0
Fetching: claide-1.0.2.gem (100%)
Successfully installed claide-1.0.2
Fetching: colored2-3.1.2.gem (100%)
Successfully installed colored2-3.1.2
Fetching: nanaimo-0.2.5.gem (100%)
Successfully installed nanaimo-0.2.5
Fetching: xcodeproj-1.5.7.gem (100%)
Successfully installed xcodeproj-1.5.7
Parsing documentation for atomos-0.1.2
Installing ri documentation for atomos-0.1.2
Parsing documentation for CFPropertyList-3.0.0
Installing ri documentation for CFPropertyList-3.0.0
Parsing documentation for claide-1.0.2
Installing ri documentation for claide-1.0.2
Parsing documentation for colored2-3.1.2
Installing ri documentation for colored2-3.1.2
Parsing documentation for nanaimo-0.2.5
Installing ri documentation for nanaimo-0.2.5
Parsing documentation for xcodeproj-1.5.7
Installing ri documentation for xcodeproj-1.5.7
Done installing documentation for atomos, CFPropertyList, claide, colored2, nanaimo, xcodeproj after 3 seconds
6 gems installed
>```

#### rvm的安装

>* 安装rvm,ruby vision manager
>```
>$curl -L get.rvm.io | bash -s stable
>$source ~/.bashrc
$source ~/.bash_profile  
>```
>
>* $rvm list known 
>```
> // 查看所有的ruby版本,如果有明确想升级的版本,可不查看
```
>
>* $rvm install 2.3.0  // 开始安装
>

# see also 

>* [使用CocoaPods开发并打包静态库](http://www.cnblogs.com/brycezhang/p/4117180.html)
>
>* [cocoaPods 开发打包静态库](https://blog.csdn.net/Alpaca12/article/details/78386616)
>
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost pod_lib_create cocoaPods开发打包静态库,比我之前自己搭建的模板更方便 -t CocoaTouchStaticLibrary
> #原来""的参数，需要自己加上""
```

