---
layout: post
title: pod_lib_create
date: 2018-04-26
tag: 
    - CocoaTouchStaticLibrary
    - CocoaPods
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 采用 `pod lib` 开发并打包静态库,比我之前自己搭建的模板更方便
---


# 前言

之前我自己搭建了一个模板，采用[git_subtree](https://kunnan.github.io/2018/04/25/git_subtree/)进行管理；最近我喜欢上了使用cocoaPods开发打包静态库.

>* [以前自己搭建的静态库模板](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>```
>开发一个用git_subtree管理依赖关系的静态类库给其他人使用，打包成.a文件。
>```
>* 今天的重点就是换成 `pod lib` 创建开发静态类库的工程模版，并利用`pod package `进行打包；
>```
>当然如果你想开源，
>1） 使用：pod lib lint验证格式
>pod lib lint KNPodlib.podspec --verbose --allow-warnings
>2） 利用pod trunk push 进行发布
>pod trunk push  KNPodlib.podspec --verbose --allow-warnings
>```
>
>* pwd
>```
>/Users/devzkn/code/cocoapodDemo/cocoapodStatic
>```
>
>* [github code](https://github.com/zhangkn/KNPodlib)
>

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
>* [基于pod自动创建`pod lib create`](https://cocoapods.org/pods/KNPodlib/)
>
>


#  pod lib --help


#### 1、 Develop pods

>* Commands:
>
>```
>    + create    Creates a new Pod
>     + lint      Validates a Pod
>```
>

#### 2、 [Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

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

#### 3、The Pod Lib Create Template


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




# 实践


#### 0、pod lib create KNPodlib

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
To learn more about creating a new pod, see `http://guides.cocoapods.org/making/making-a-cocoapod`.
>```

#### 1、The Pod Lib Create Template

>* tree -L 3
>```
>.
├── Example # This is the generated Demo & Testing bundle
│   ├── KNPodlib
│   │   ├── Base.lproj
│   │   ├── Images.xcassets
│   │   ├── KNAppDelegate.h
│   │   ├── KNAppDelegate.m
│   │   ├── KNPodlib-Info.plist
│   │   ├── KNPodlib-Prefix.pch
│   │   ├── KNViewController.h
│   │   ├── KNViewController.m
│   │   ├── en.lproj
│   │   └── main.m
│   ├── KNPodlib.xcodeproj
│   │   ├── project.pbxproj
│   │   ├── project.xcworkspace
│   │   └── xcshareddata
│   ├── KNPodlib.xcworkspace
│   │   ├── contents.xcworkspacedata
│   │   └── xcuserdata
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   │   ├── Expecta
│   │   ├── Expecta+Snapshots
│   │   ├── FBSnapshotTestCase
│   │   ├── Headers
│   │   ├── Local\ Podspecs
│   │   ├── Manifest.lock
│   │   ├── Pods.xcodeproj
│   │   ├── Specta
│   │   └── Target\ Support\ Files
│   └── Tests
│       ├── Tests-Info.plist
│       ├── Tests-Prefix.pch
│       ├── Tests.m
│       └── en.lproj
├── KNPodlib #This is where you place your library's classes
│   ├── Assets
│   └── Classes
│       └── ReplaceMe.m # a single file to to ensure compilation works initially
├── KNPodlib.podspec
├── LICENSE #defaulting to the MIT License.
├── README.md
├── .travis.yml #a setup file for travis-ci.
└── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj #a symlink to your Pod's project for Carthage support
>```

>* /Users/devzkn/code/cocoapodDemo/cocoapodStatic/KNPodlib/Example/Pods/Target Support Files/KNPodlib
>

>* [open -e .travis.yml](https://github.com/zhangkn/KNPodlib/blob/master/.travis.yml)
>
>```
># references:
# * https://www.objc.io/issues/6-build-tools/travis-ci/
# * https://github.com/supermarin/xcpretty#usage
osx_image: xcode7.3
language: objective-c
# cache: cocoapods
# podfile: Example/Podfile
# before_install:
# - gem install cocoapods # Since Travis is not always on latest version
# - pod install --project-directory=Example
script:
- set -o pipefail && xcodebuild test -enableCodeCoverage YES -workspace Example/KNPodlib.xcworkspace -scheme KNPodlib-Example -sdk iphonesimulator9.3 ONLY_ACTIVE_ARCH=NO | xcpretty
- pod lib lint
>```

#### 3、Putting your Library Together


>* cat .gitignore
><script src="https://gist.github.com/zhangkn/d8c8e4907975abfcf6f5686c6d664baa.js"></script>
>
>
>* edit your Podspec metadata
>```
> change your README and Podspec.
>```
>* Development Pods
>```
>1) Development Pods are different from normal CocoaPods in that they are symlinked files,so you can work on your library from inside Xcode.
>2)  Your demo & tests will need to include references to headers using the #import <MyLib/XYZ.h> format.
>```

>* [!] Note:运行Pod install，让demo程序加载新建的类
>```
>Due to a Development Pods implementation detail, when you add new/existing files to Pod/Classes or Pod/Assets or update your podspec, you should run pod install or pod update
>只要新增加类/资源文件或依赖的三方库都需要重新运行Pod install来应用更新
>```
>
>
>* cd Example ;执行pod install，让demo项目安装依赖项并更新配置
>```
> pod install --no-repo-update
> devzkndeMacBook-Pro:Example devzkn$ pod install --no-repo-update
Analyzing dependencies
Fetching podspec for `KNPodlib` from `../`
Sending stats
See `https://guides.cocoapods.org/syntax/podfile.html#platform`.
>```
>*   使用了 s.resource_bundles，之后，会自动创建KNPodlib.bundle
>```
>devzkndeMacBook-Pro:KNPodlib devzkn$ tree -L 2
>.
├── KNPodlib.bundle
│   ├── HCPCMPayProgress.nib
│   ├── Info.plist
│   ├── KNFeedbackViewController.nib
│   ├── deleteX.png
│   ├── hebaoGrayPoint.png
│   ├── hebaoWhitePoint.png
│   └── store_add.png
└── KNPodlib.framework
    ├── Headers
    ├── Info.plist
    ├── KNPodlib
    ├── Modules
    └── _CodeSignature
>```
>
>

# 开发细节

#### 1、 public_header_files 配置

>*  s.public_header_files 
>```
>   s.public_header_files = 'Classes/PublicInterface/*.h'
>```
>
>*  KNPodlib/KNPodlib-umbrella.h
>
>```
>#ifdef __OBJC__
#import <UIKit/UIKit.h>
#else
#ifndef FOUNDATION_EXPORT
#if defined(__cplusplus)
#define FOUNDATION_EXPORT extern "C"
#else
#define FOUNDATION_EXPORT extern
#endif
#endif
#endif
#import "HCPEnvrionmentalVariables.h"
#import "KNFeedbackViewController.h"
#import "KNTestWebViewController.h"
FOUNDATION_EXPORT double KNPodlibVersionNumber;
FOUNDATION_EXPORT const unsigned char KNPodlibVersionString[];
>```
>
>



#### 2、提交本地代码库


>* devzkndeMacBook-Pro:KNPodlib devzkn$ kngitinit git@github.com:zhangkn/KNPodlib.git
>
>
>
>* 提交源码，并打tag。
>
>```
>git add .
git commit -a -m 'v0.1.0'
git tag -a 0.1.0 -m 'v0.1.0'
#git tag -a v1.0 -m 'xxx' 
git push origin --tags
To github.com:zhangkn/KNPodlib.git
 * [new tag]         0.1.0 -> 0.1.0
 * [new tag]         0.1.1 -> 0.1.1
>```
>
>
#  Deploying your Library



#### 1、验证类库
>* First you should check if the Podspec lints correctly
>
>```
>pod lib lint and pod spec lint
>1)The difference between them is that 
>2) pod lib lint does not access the network, whereas pod spec lint checks the external repo and associated tag.
>```
>* devzkndeMacBook-Pro:KNPodlib devzkn$  pod lib lint KNPodlib.podspec --verbose --allow-warnings
>```
>KNPodlib passed validation.
>```
>

# 打包类库(只有完成这一个步骤，才达到静态库的目的，否则就是开源的了)

>* 没有执行打包类库这个步骤，直接deploy to the public的效果
>```
>├── Podfile
├── Podfile.lock
├── Pods
│   ├── Headers
│   ├── KNPodlib
│   │   ├── Assets
│   │   │   ├── deleteX.png
│   │   │   ├── hebaoGrayPoint.png
│   │   │   ├── hebaoWhitePoint.png
│   │   │   └── store_add.png
│   │   ├── Classes
│   │   │   ├── PublicInterface
│   │   │   │   ├── HCPEnvrionmentalVariables.h
│   │   │   │   ├── KNBaseViewController.h
│   │   │   │   ├── KNBaseWebViewController.h
│   │   │   │   ├── KNFeedbackViewController.h
│   │   │   │   └── KNTestWebViewController.h
│   │   │   ├── ReplaceMe.m
│   │   │   └── main
│   │   │       ├── knFeedback
│   │   │       │   ├── model
│   │   │       │   ├── vc
│   │   │       │   └── view
│   │   │       ├── tool
│   │   │       │   ├── HCPEnvrionmentalVariables.m
│   │   │       │   ├── HSSingleton.h
│   │   │       │   ├── IPLoadingTool.h
│   │   │       │   ├── IPLoadingTool.m
│   │   │       │   ├── KNResourceTool.h
│   │   │       │   ├── KNResourceTool.m
│   │   │       │   ├── const
│   │   │       │   └── view
│   │   │       └── webview
│   │   │           ├── BaseWebView
│   │   │           ├── javaScriptContext
│   │   │           └── progress
│   │   ├── LICENSE
│   │   └── README.md
>```
>
>* 执行pod package KNPodlib.podspec --force 之后的效果
>
>
>* 执行pod package KNPodlib.podspec --force 之前的准备
>```
>devzkndeMacBook-Pro:KNPodlib devzkn$ git tag -a 0.1.8 -m "0.1.8"
>git push origin --tags
> pod package KNPodlib.podspec --force
> tree -L 4
> ── KNPodlib-0.1.9
│   ├── KNPodlib.podspec
│   ├── build
│   │   └── Pods.build
│   │       ├── Release-iphoneos
│   │       └── Release-iphonesimulator
│   └── ios
│       └── KNPodlib.framework
│           ├── Headers -> Versions/Current/Headers
│           ├── KNPodlib -> Versions/Current/KNPodlib
│           ├── Resources -> Versions/Current/Resources
│           └── Versions
pod lib lint KNPodlib.podspec --verbose --allow-warnings
pod trunk push  KNPodlib.podspec --verbose --allow-warnings
>```

# [ deploy to the public](https://guides.cocoapods.org/making/getting-setup-with-trunk)

`pod trunk push [NAME.podspec]` will deploy your Podspec to Trunk and make it publicly available. You can also deploy Podspecs to your own private specs repo with `pod repo push REPO [NAME.podspec]`.


>* Deploying a library
>```
>If you're deploying an Open Source library to trunk, you cannot have CocoaPods warnings. You can have Xcode warnings though. You should continue to the getting started with trunk guide to deploy to the public.
>pod repo push SPEC_REPO *.podspec --verbose
>```

#### 1、deploy Podspecs to your own private specs repo

`pod repo push REPO [NAME.podspec]`.

>* [ Private Specs Repos](https://guides.cocoapods.org/making/private-cocoapods)
>
>
>
>* Deploying with push:
>```
>1) Lints your Podspec locally. You can lint at any time with  pod spec lint [NAME.podspec]
>2) A successful lint pushes your Podspec to Trunk or your private specs repo
>3) Trunk will publish a canonical JSON representation of your Podspec
>```

>
>* pod repo push SPEC_REPO *.podspec --verbose
>```
>If you're deploying to a private Specs repo, you will need to have already added that repo.  use this command to deploy:
>pod repo push SPEC_REPO *.podspec --verbose
>```

#### 2、 deploy your Podspec to Trunk and make it publicly available

>* pod trunk push [NAME.podspec] —allow-warnings 
>```
>pod trunk push —allow-warnings 
>```
>
>
>* devzkndeMacBook-Pro:KNPodlib devzkn$ pod trunk push  KNPodlib.podspec --verbose --allow-warnings
>
>```
>Updating spec repo `master`
>CocoaPods 1.5.0 is available.
>    - April 26th, 20:21: Push for `KNPodlib 0.1.3' has been pushed (0.801533271 s).
>```

>* [Data URL](https://raw.githubusercontent.com/CocoaPods/Specs/6c07ec30e394434252110b79d56afa6b7c928b3c/Specs/3/9/a/KNPodlib/0.1.3/KNPodlib.podspec.json)
><script src="https://gist.github.com/zhangkn/9c1a28b7ac22300e7f848d6d70dd0e16.js"></script>
>
>
>* [Page on CocoaPods.org](https://cocoapods.org/pods/KNPodlib)
>
>* [Documentation](http://cocoadocs.org/docsets/KNPodlib/0.1.3)
>
>* [GitHub Repo](https://github.com/zhangkn/KNPodlib)
>
>* [Maintained by zhangkn](https://cocoapods.org/owners/30966)
>
>
>*  pod trunk info KNPodlib
>
>```
>devzkndeMacBook-Pro:KNPodlib devzkn$ pod trunk info KNPodlib
KNPodlib
    - Versions:
      - 0.1.3 (2018-04-27 02:21:06 UTC)
    - Owners:
      - zhangkn <developerkunnan@gmail.com>
>```
>* 注意事项
>```
>1) 以后再次更新只需要把你的项目打一个tag，然后修改.podspec文件中的版本，接着提交到cocoapods官方.
>2) 手动验证 Pod 时使用了 --use-libraries(引用了.a文件的时候需要使用) 或 --allow-warnings 等修饰符，那么发布的时候也应该使用相同的字段修饰，否则出现相同的报错
>```
>
#### 3、Adding other people as contributors

The first person to push a Podspec version to Trunk can add other maintainers. 

>* For example, to add kyle@cocoapods.org to the library ARAnalytics:
>```
>$ pod trunk add-owner ARAnalytics kyle@cocoapods.org
>```
>

#### 4、Claiming an existing library

you can use our [Claims form](https://trunk.cocoapods.org/claims/new) to say that you are the owner or maintainer of a collection of libraries

>* [Claim your Pod](https://trunk.cocoapods.org/claims/new)
>```
>OWNER NAME:
>OWNER EMAIL:
>CLAIM POD:
>```
>![](https://ws2.sinaimg.cn/large/006tNc79gy1fqr1s3u069j31kw0jttan.jpg)
>* Note you can use` pod trunk info [pod] `to get information on a pod and `pod trunk me `can be used to verify your local account.
>
>* [KNPodlib claims](https://trunk.cocoapods.org/claims/disputes/new?claimer_email=developerkunnan%40gmail.com&pods%5B%5D=KNPodlib)
>
>

# 关于 Pod 库的资源引用 resource_bundles or resources

We strongly recommend library developers to adopt resource bundles as there can be name collisions using the resources attribute. Moreover, resources specified with this attribute are copied directly to the client target and therefore they are not optimised by Xcode.


#### 1.1 resource_bundles

CocoaPods 官方强烈推荐使用 resource_bundles，因为用 key-value 可以避免相同名称资源的名称冲突。


>*  存放的位置: 隔离了各个库或者一个库下的资源包
>```
>devzkndeMacBook-Pro:KNPodlib_Example.app devzkn$ tree -L 4
.
├── Base.lproj
│   └── LaunchScreen.storyboardc
│       ├── 01J-lp-oVM-view-Ze5-6b-2t3.nib
│       ├── Info.plist
│       └── UIViewController-01J-lp-oVM.nib
├── Frameworks
│   └── KNPodlib.framework
│       ├── HCPCMPayProgress.nib
│       ├── Info.plist
│       ├── KNFeedbackViewController.nib
│       ├── KNPodlib
│       ├── KNPodlib.bundle
│       │   ├── Info.plist
│       │   ├── deleteX.png
│       │   ├── hebaoGrayPoint.png
│       │   ├── hebaoWhitePoint.png
│       │   └── store_add.png
│       └── _CodeSignature
│           └── CodeResources
>```
>* [需要带上 .bundle 文件的路径,获取图片](https://github.com/zhangkn/KNPodlib/blob/master/Classes/main/tool/KNResourceTool.m)
><script src="https://gist.github.com/zhangkn/8b084dfd7d34e775cc09aefad42ca7c9.js"></script>

#### 1.2 resources

使用 resources 来指定资源，被指定的资源只会简单的被 copy 到目标工程中（主工程）。


>* resources
>
>```
spec.ios.resource_bundle = { 'MapBox' => 'MapView/Map/Resources/*.png' }
```

>* Examples
>
>```
>spec.resource = 'Resources/HockeySDK.bundle'
>spec.resources = ['Images/*.png', 'Sounds/*']
>```

>* [syntax - podspec](https://guides.cocoapods.org/syntax/podspec.html)
>
>
>* 读取图片的方式和平常使用的方式不同，要先获取 Bundle;可以使用相对方式获取
>```
>UIImage *image = [UIImage imageNamed:@"some-image"
                                inBundle:[NSBundle bundleForClass:[self class]]
           compatibleWithTraitCollection:nil];
>```
>
>

#### 2 对于 Pods 库的资源，同样可以使用 .xcassets 管理。
 resources 和 resource_bundles 都可以很好的支持 .xcassets 的引用


#### 3、小结

>* resource_bundles 优点：
>```
>可以使用 .xcassets 指定资源文件
可以避免每个库和主工程之间的同名资源冲突
>```
>
>* resource_bundles 缺点：我觉得还好
>```
>获取图片时可能需要使用硬编码的形式来获取：[[NSBundle bundleForClass:[self class]].resourcePath stringByAppendingPathComponent:@"/SubModule_Use_Bundle.bundle"]
>```
>
>* resources 优点：
>
>```
>可以使用 .xcassets 指定资源文件
>```
>
>* resources 缺点：
>```
>会导致每个库和主工程之间的同名资源冲突
不需要用硬编码方式获取图片：[NSBundle bundleForClass:[self class]] compatibleWithTraitCollection:nil];
>```
>
>


# [Demo](https://github.com/zhangkn/KNPodlib)


>* [KNPodlibDemo](https://github.com/kunnan/KNPodlibDemo)
>
>
>* 修改podfile之后，只有执行pod  install之后才能生效
>

# 知识补充：pod package


>*  Package a podspec into a static library.
>```
使用一个cocoapods的插件cocoapods-packager来完成类库的打包.
```

>* 安装打包插件:gem install cocoapods-packager
>```
>sudo gem install cocoapods-packager
>Done installing documentation for concurrent-ruby, i18n, thread_safe, tzinfo, activesupport, nap, fuzzy_match, cocoapods-core, claide, cocoapods-deintegrate, cocoapods-downloader, cocoapods-plugins, cocoapods-search, cocoapods-stats, netrc, cocoapods-trunk, cocoapods-try, molinillo, atomos, CFPropertyList, colored2, nanaimo, xcodeproj, escape, fourflusher, gh_inspector, ruby-macho, cocoapods, cocoapods-packager after 20 seconds
29 gems installed
>```


>* pod package --help
>```
>    $ pod package NAME [SOURCE]
>```

>* Options:
><script src="https://gist.github.com/zhangkn/62bcfd228864db619a99fe7363002b59.js"></script>
> 
>*  pod package KNPodlib.podspec --force
>```
>Building framework KNPodlib (0.1.5) with configuration Release
Mangling symbols
Building mangled framework
// 这个时候如果报错：Unable to run command 'StripNIB，因为source_files包含了xib.
├── KNPodlib-0.1.5
│   ├── KNPodlib.podspec
│   ├── build
│   │   └── Pods.build
│   │       ├── Release-iphoneos
│   │       │   ├── KNPodlib-KNPodlib.build
│   │       │   ├── KNPodlib.build
│   │       │   └── Pods-packager.build
│   │       └── Release-iphonesimulator
│   │           ├── KNPodlib-KNPodlib.build
│   │           ├── KNPodlib.build
│   │           └── Pods-packager.build
│   └── ios
│       └── KNPodlib.framework
│           ├── Headers -> Versions/Current/Headers
│           ├── KNPodlib -> Versions/Current/KNPodlib
│           ├── Resources -> Versions/Current/Resources
│           └── Versions
│               ├── A
│               └── Current -> A
>```
>

# Q&A

#### q0、Unable to run command 'StripNIB KNFeedbackViewController~ipad.nib' - this target might include its own product.


>*   s.source_files = 'Classes/**/*'
>```
>xib不能当成源文件(即s.source_files),否则 pod package KNPodlib.podspec --force 会报以上错误。
>应当修改为：
>s.source_files = 'Classes/**/*.{h,m}'
>```

>* Cocoapod compilation fails when loading .xib file
>```
>s.resource = "Project/**/*.{png,bundle,xib,nib}"
>```
>
>* resource_bundles
>
>```
>   "resource_bundles": {
     "KNPodlib": [
-      "Assets/*.png"
+      "Assets/*.png",
+      "Classes/**/*.xib"
     ]
   },
>```
>
>*  增加"Classes/**/*.xib" 之后， KNPodlib.bundle的资源文件情况的变化
>```
>    ├── KNPodlib.bundle
    │   ├── Info.plist
    │   ├── deleteX.png
    │   ├── hebaoGrayPoint.png
    │   ├── hebaoWhitePoint.png
    │   └── store_add.png
    <!--增加之后的情况-->
        ├── KNPodlib.bundle
    │   ├── HCPCMPayProgress.nib
    │   ├── Info.plist
    │   ├── KNFeedbackViewController.nib
    │   ├── deleteX.png
    │   ├── hebaoGrayPoint.png
    │   ├── hebaoWhitePoint.png
    │   └── store_add.png
>```
>



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
>
>
#### Q2、Unsupported Swift Version

>* Unsupported Swift Version
>```
>The target “FBSnapshotTestCase” contains source code developed with Swift 2.x. Xcode 9 does not support building or migrating Swift 2.x targets.
Use Xcode 8.x to migrate the code to Swift 3.
>```
>
>* A: 我这里直接采用废弃FBSnapshotTestCase，从podfile删除FBSnapshotTestCase
>
>

#### q3、引用的静态库资源图片没找到

>* A:
><script src="https://gist.github.com/zhangkn/8b084dfd7d34e775cc09aefad42ca7c9.js"></script>

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
>

# see also 

>* [关于 Pod 库的资源引用 resource_bundles or resources](http://zhoulingyu.com/2018/02/02/pod-resource-reference/)
>
>* [Developing private static library for iOS with CocoaPods](https://kunnan.github.io/2017/03/10/making_private_cocoapods/)
>
>* [Automatic build of static library for iOS and many architectures](http://blog.sigmapoint.pl/automatic-build-of-static-library-for-ios-for-many-architectures/)
>* [avoiding-dependency-collisions-in-ios-static-library-managed-by-cocoapods/](http://blog.sigmapoint.pl/avoiding-dependency-collisions-in-ios-static-library-managed-by-cocoapods/)
>* [使用CocoaPods开发并打包静态库](http://www.cnblogs.com/brycezhang/p/4117180.html)
>* [cocoaPods 开发打包静态库](https://blog.csdn.net/Alpaca12/article/details/78386616)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost pod_lib_create cocoaPods开发打包静态库,比我之前自己搭建的模板更方便 -t CocoaTouchStaticLibrary
> #原来""的参数，需要自己加上""
```


