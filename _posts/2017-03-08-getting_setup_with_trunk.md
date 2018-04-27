---
layout:     post
title:      getting_setup_with_trunk
subtitle:   guide to deploy to the public  pod repo :`pod trunk push [NAME.podspec] `
date:       2017-03-08
author:     
header-img: img/post-bg-ios10.jpg
catalog: true
tags:
    - iOS
    - CocoaPods
    - Git
---

# 前言

以前,我经常以提供[静态库的方式](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)给第三方使用，最近发现使用CocoaPods会更方便维护管理。

>* [setup_Cocoa_Touch_Static_Library](https://kunnan.github.io/2018/04/25/setup_Cocoa_Touch_Static_Library/)
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
>```


#### 自己搭建模板和pod的模板的比较

>* 目前静态库的管理我会结合CocoaPods、demo的xcodeproj 和[git_subtree](https://kunnan.github.io/2018/04/25/git_subtree/)
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree push --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master 
>.
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
│   ├── KNCocoaTouchStaticLibrary.podspec
│   ├── KNCocoaTouchStaticLibrary.xcodeproj
│   ├── KNStaticBundle
│   ├── LICENSE
│   └── README.md
└── README.md
>```
>
>* [cocoaPods开发并打包静态库:  pod lib create](https://kunnan.github.io/2018/04/26/pod_lib_create/)
>```
>基于Pod自动创建静态库,这个目前采用xcworkspace和Pods进行管理依赖很方便。
>devzkndeMacBook-Pro:KNPodlib devzkn$ tree -L 2
.
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
├── KNPodlib.podspec # the Podspec for your Library.
├── LICENSE
├── README.md #  a default README in markdown.
└── _Pods.xcodeproj -> Example/Pods/Pods.xcodeproj
>```
>
>
>* 可以看出，pod lib 也是可以采用Bundle的方式
>```
>├── KNPodlib
│   ├── Assets
  # s.resource_bundles = {
  #   'KNPodlib' => ['KNPodlib/Assets/*.png']
  # }
>```

#### ~/.cocoapods/repos/master

>* CocoaPods本地目录 ~/.cocoapods/repos/master
>```
>devzkndeMBP:master devzkn$ tree -L 3
.
├── CocoaPods-version.yml
├── README.md
└── Specs
    ├── 0
    │   ├── 0
    │   ├── 1
>```
	
>* 隐藏文件
```
devzkndeMBP:master devzkn$ ls -a
.			..			.DS_Store		.git			.gitignore		CocoaPods-version.yml	README.md		Specs
```
	
 
>*  git remote -v: `master` 是一个 GitHub仓库
>```
>//显示所有远程仓库
>devzkndeMBP:master devzkn$  git remote -v
origin	https://github.com/CocoaPods/Specs.git (fetch)
origin	https://github.com/CocoaPods/Specs.git (push)
```
	
>* `Specs`文件夹:很多框架以及版本号
>```
>devzkndeMBP:Specs devzkn$ tree -L 4
.
├── 0
│   ├── 0
│   │   ├── 0
│   │   │   ├── CAIStatusBar
>```

>* /Users/devzkn/.cocoapods/repos/master/Specs/0/0/0/CAIStatusBar/0.0.1/
>```
>devzkndeMBP:CAIStatusBar devzkn$ tree -L 4
.
└── 0.0.1
    └── CAIStatusBar.podspec.json
>```

>* pod search CAIStatusBar:搜索Specs文件夹中的框架信息
>
>```
>Creating search index for spec repo 'AliBCSpecs'.. Done!
-> CAIStatusBar (0.0.1)
   A simple indicator
   pod 'CAIStatusBar', '~> 0.0.1' 每个版本号对应的一个json文件,描述了每个对应版本的框架的信息、配置、及源码下载地。
>```

>* 在CocoaPods发布框架时要在 `master` 仓库中添加我们的仓库描述信息，然后push到远程仓库中。
>```
>用`pod`命令进行操作
>```



# pod

>* pod --help
>
>```
>    + trunk         Interact with the CocoaPods API (e.g. publishing new specs)
>```


####  pod trunk --help

>* CocoaPods Trunk
>
>```
>CocoaPods Trunk is an authentication and CocoaPods API service. To publish new or updated libraries to CocoaPods for public release you will need to be registered with Trunk and have a valid Trunk session on your current device. 
>```

>* pod trunk COMMAND
>```
      Interact with the CocoaPods API (e.g. publishing new specs)
```

>*  pod trunk --help

>* Commands:
>
```
    + add-owner      Add an owner to a pod
    + delete         Deletes a version of a pod.
    + deprecate      Deprecates a pod.
    + info           Returns information about a Pod.
    + me             Display information about your sessions
    + push           Publish a podspec
    + register       Manage sessions
    + remove-owner   Remove an owner from a pod
```

>* Options:

```
    --silent         Show nothing
    --verbose        Show more debugging information
    --no-ansi        Show output without ANSI codes
    --help           Show help banner of specified command
```


####    pod spec --help
 Manage pod specs


>* pod spec --help
>
>* Commands:
>
```
    + cat       Prints a spec file
    + create    Create spec file stub.
    + edit      Edit a spec file
    + lint      Validates a spec file
    + which     Prints the path of the given spec
```

>* pod spec cat
>```
>    $ pod spec cat [QUERY]
      Prints the content of the podspec(s) whose name matches `QUERY` to standard
      output.
Options:
    --regex      Interpret the `QUERY` as a regular expression
    --show-all   Pick from all versions of the given podspec
    --silent     Show nothing
    --verbose    Show more debugging information
    --no-ansi    Show output without ANSI codes
    --help       Show help banner of specified command
>```


# 创建 CocoaPods 公有仓库


#### 1、注册 CocoaPods 账号:  Manage sessions

First sign up for an account with your email address. This begins a session on your current device.

>* 使用终端注册, `email` 用你的 `GitHub` 邮箱: pod trunk register mail name --description='macbook air'  --verbose
>
>```
>devzkndeMacBook-Pro:Debug-iphoneos devzkn$  pod trunk register @gmail.com name  --verbose
opening connection to trunk.cocoapods.org:443...
>```


>* [CocoaPods] Confirm your registration.
>```
>Hi,
Please confirm your registration with CocoaPods by clicking the following link:
https://trunk.cocoapods.org/sessions/verify/
If you did not request this you do not need to take any further action.
Kind regards, the CocoaPods team
>```	
>* ACE, YOU'RE SET UP.
>```
You can go back to your terminal now.
>```
	
>* 	pod trunk me: Display information about your sessions
>
>```
>list your sessions
devzkndeMacBook-Pro:Debug-iphoneos devzkn$ pod trunk me
  - Name:    
>```
>
>


#### 2、创建Git仓库

>* [KNIosCommonTool.git](https://github.com/zhangkn/KNIosCommonTool)
>```
>devzkndeMacBook-Pro:KNIosCommonTool devzkn$ kngitinit git@github.com:zhangkn/KNIosCommonTool.git
>```


######  2、1 [GitHub](https://github.com) 上创建一个公开项目，项目中必须包含这几个文件

>* 如果使用pod lib create的时候，会自动生成模板(推荐使用)
>```
├── Example #代码使用样例,测试功能
│   ├── KNPodlib
│   ├── KNPodlib.xcodeproj
│   ├── KNPodlib.xcworkspace
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   └── Tests
├── KNPodlib # 开发区域
│   ├── Assets
│   └── Classes
├── KNPodlib.podspec # the Podspec for your Library.
├── LICENSE
├── README.md #  a default README in markdown.
>```


#### 3 [创建`.podspec` Create spec file stub.](https://guides.cocoapods.org/syntax/podspec.html#specification)

>* curl -O url 下载开源许可证---不太靠谱
>```
>curl -O https://github.com/zhangkn/KNPodlib/blob/master/LICENSE
>```
>
>
>*   pod spec create [NAME|https://github.com/USER/REPO]

>* [`.podspec` 是用 Ruby 的配置文件，描述你项目的信息。](https://guides.cocoapods.org/syntax/podspec.html#specification)
>```
> A specification describes a version of Pod library
> A stub specification file can be generated by the pod spec create command.
>```
>
>* cd仓库目录下 pod spec create
```
devzkndeMacBook-Pro:KNCocoaTouchStaticLibrary devzkn$ pod spec create KNCocoaTouchStaticLibrary
>Specification created at KNCocoaTouchStaticLibrary.podspec
```	
><script src="https://gist.github.com/zhangkn/02cb3a7413b58c5db7c96797f0bd40b1.js"></script>


>* 使用支持yaml 格式的ide进行编辑：sublime text、Xcode
>
>
>
>

>* 验证 `.podspec` 文件的格式是否正确，

>*  pod lib lint KNPodlib.podspec --verbose --allow-warnings
>```
>	 pod lib lint KNIosCommonTool.podspec --verbose --allow-warnings
>```
>
>

>* `'echo "2.3" > .swift-version'`
>

#### 4、 给仓库打标签，因为s.source 是从tag 获取的

>* s.source 的格式
>```
>  s.source       = { :git => "https://github.com/zhangkn/KNCocoaTouchStaticLibrary.git", :tag => "#{s.version}" } ##你的仓库地址，不能用SSH地址
>```

>* 	创建标签
>```
>git tag -a 1.0.0 -m '标签说明' 
>	 git push origin --tags
>```



#### 5、 发布`.podspec` Deploying a library



`pod trunk push [NAME.podspec] `will deploy your Podspec to Trunk and make it publicly available.
You can also deploy Podspecs to your own private specs repo with` pod repo push REPO [NAME.podspec]`.


>* pod trunk push --help
>```
>pod trunk push [PATH]
>    Publish the podspec at `PATH` to make it available to all users of the ‘master’
      spec-repo. If `PATH` is not provided, defaults to the current directory.
>```
>
>* Options:
>```
>    --allow-warnings           Allows push even if there are lint warnings
    --use-libraries            Linter uses static libraries to install the spec
    --swift-version=VERSION    The SWIFT_VERSION that should be used to lint the spec.
                               This takes precedence over a .swift-version file.
    --skip-import-validation   Lint skips validating that the pod can be imported
    --skip-tests               Lint skips building and running tests during validation
    --silent                   Show nothing
    --verbose                  Show more debugging information
    --no-ansi                  Show output without ANSI codes
    --help                     Show help banner of specified command
>```
>
>


>* 在仓库目录下执行,发布到公有的speecs上: `pod trunk push KNIosCommonTool.podspec`
>
>```
>1. 更新本地 pods 库 `~/.cocoaPods.repo/master`
>- 验证`.podspec`格式是否正确
>- 将 `.podspec` 文件转成 JSON 格式
>- 对 `master` 仓库 进行合并、提交.[master仓库地址](https://github.com/CocoaPods/Specs) 
```

>* [进入的Pods查看自己的仓库信息](https://cocoapods.org/pods/KNIosCommonTool)
>```
>https://cocoapods.org/pods/KNIosCommonTool
>```
	 	 


#### 使用仓库

>* `pod setup`在终端更新本地pods仓库信息
>```
>Setting up CocoaPods master repo
>```
>


查询仓库
	
	$ pod search BYPhoneNumTF
---
	-> BYPhoneNumTF (1.0.0)
	   A delightful TextField of PhoneNumber
	   pod 'BYPhoneNumTF', '~> 1.0.0'
	   - Homepage: https://github.com/qiubaiying/BYPhoneNumTF
	   - Source:   https://github.com/qiubaiying/BYPhoneNumTF.git
	   - Versions: 1.0.0, 0.0.1 [BYPhoneNumTF repo]
	(END)

若出现仓库信息说明已经成功了，这时候你就可以在 `Podfile` 添加、使用自己的仓库了 `pod 'BYPhoneNumTF', '~> 1.0.0'`

![](https://ww1.sinaimg.cn/large/006tNbRwgy1fdedvficvaj30fu0loaex.jpg)

#### 更新维护

当你的代码更新维护后，就需要重写发布，流程是：

- 更新`BYPhoneNumTF.podspec`中的版本号
- 打上标签推送远程
- `pod trunk push BYPhoneNumTF.podspec` 推送到pods仓库

更新后你就可以在 [CocoaPods Master Repo](https://github.com/CocoaPods/Specs) 仓库上看到自己的提交记录了。

![](https://ww4.sinaimg.cn/large/006tNbRwgy1fdfkr2l7omj31kw0d7446.jpg)

# see also

>* [ private pods](https://guides.cocoapods.org/making/private-cocoapods.html)
>
>
>* [getting-setup-with-trunk](https://guides.cocoapods.org/making/getting-setup-with-trunk)
>

>* [Podspec Syntax Reference](https://guides.cocoapods.org/syntax/podspec.html#specification)
>
>* [ios-dev-uses-cocoapods-to-package-static-libraries-relying-on-private-libraries-open-source-libraries-private-libraries-and-static-libraries.html](http://w3cgeek.com/ios-dev-uses-cocoapods-to-package-static-libraries-relying-on-private-libraries-open-source-libraries-private-libraries-and-static-libraries.html)
>
>* [使用cocoapods打包静态库(依赖私有库，开源库，私有库又包含静态库)](https://www.jianshu.com/p/9096a2eb2804)
>
>* [搭建一个提高开发效率的iOS静态库工程](https://blog.csdn.net/z929118967/article/details/73872024)
>```
>https://github.com/zhangkn/KNAPP/blob/master/README.md
>```
>* [截图反馈功能的实现](https://github.com/zhangkn/IOSStudy)
>```
> 图片的上传地址进行更换就可以应用到其他项目中（主要采用自定义View实现）。
```

>* 创建软连接: ln -s 使用相对的路径
>```
lrwxr-xr-x   1 devzkn  staff   56 Apr 27 17:23 KNIosCommonTool.podspec -> KNIosCommonTool/Podspec Metadata/KNIosCommonTool.podspec
>ps: rm -rf symbolic_name 注意不是rm -rf symbolic_name/
>```

