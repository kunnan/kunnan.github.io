---
layout:     post
title:      CocoaPods公有仓库的创建
subtitle:   手把手教你创建 CocoaPods 公有仓库
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
>
>
>

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
>Creating search index for spec repo 'artsy'.. Done!
>Creating search index for spec repo 'master'.. Done!
>Creating search index for spec repo 'specta'.. Done!
[!] Skipping `OCastReferenceDriver` because the podspec contains errors.
[!] Skipping `Specta` because the podspec contains errors.
[!] Skipping `Specta.xcworkspace` because the podspec contains errors.
[!] Skipping `misc` because the podspec contains errors.
-> CAIStatusBar (0.0.1)
   A simple indicator
   pod 'CAIStatusBar', '~> 0.0.1' 每个版本号对应的一个json文件,描述了每个对应版本的框架的信息、配置、及源码下载地。
   - Homepage: https://github.com/apple5566/CAIStatusBar.git
   - Source:   https://github.com/apple5566/CAIStatusBar.git
   - Versions: 0.0.1 [master repo]
>```

>* 在CocoaPods发布框架时要在 `master` 仓库中添加我们的仓库描述信息，然后push到远程仓库中。
>```
>用`pod`命令进行操作
>```

下面将一步步把我封装的一个[简单的静态库：提供反馈模板和webview模板](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)上传到 Cocoapods 公有仓库中。




# pod

>* pod --help
>
>```
>    + trunk         Interact with the CocoaPods API (e.g. publishing new specs)
>```


####  pod trunk --help

    $ pod trunk COMMAND
      Interact with the CocoaPods API (e.g. publishing new specs)


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


#### 注册 CocoaPods 账号:  Manage sessions

>* 使用终端注册, `email` 用你的 `GitHub` 邮箱: pod trunk register
>
>```
>devzkndeMacBook-Pro:Debug-iphoneos devzkn$  pod trunk register @gmail.com name  --verbose
opening connection to trunk.cocoapods.org:443...
opened
starting SSL for trunk.cocoapods.org:443...
SSL established
<- "POST /api/v1/sessions HTTP/1.1\r\nContent-Type: application/json; charset=utf-8\r\nAccept: application/json; charset=utf-8\r\nUser-Agent: CocoaPods/1.4.0\r\nAccept-Encoding: gzip;q=1.0,deflate;q=0.6,identity;q=0.3\r\nHost: trunk.cocoapods.org\r\nContent-Length: 73\r\n\r\n"
<- "{\"email\":\"@gmail.com\",\"name\":\"\",\"description\":null}"
-> "HTTP/1.1 201 Created\r\n"
-> "Date: Wed, 25 Apr 2018 12:23:28 GMT\r\n"
-> "Connection: keep-alive\r\n"
-> "Strict-Transport-Security: max-age=31536000\r\n"
-> "Content-Type: application/json\r\n"
-> "Content-Length: 195\r\n"
-> "X-Content-Type-Options: nosniff\r\n"
-> "Server: thin 1.6.2 codename Doc Brown\r\n"
-> "Via: 1.1 vegur\r\n"
-> "\r\n"
reading 195 bytes...
-> "{\"created_at\":\"2018-04-25 12:23:28 UTC\",\"valid_until\":\"2018-08-31 12:23:28 UTC\",\"verified\":false,\"created_from_ip\":\"\",\"description\":null,\"token\":\"\"}"
read 195 bytes
Conn keep-alive
[!] Please verify the session by clicking the link in the verification email that has been sent to@gmail.com
//    --verbose        Show more debugging information
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
devzkndeMacBook-Pro:Debug-iphoneos devzkn$ pod trunk me
  - Name:    
  - Email:   @gmail.com
  - Since:    April 25th, 06:23
  - Pods:     None
  - Sessions:
    - April 25th, 06:23 - August 31st, 06:36. IP:
>```
	

#### 创建Git仓库

>* 这次我现在之前的静态库KNCocoaTouchStaticLibrary,是KNAPP主项目的子项目
>```
>/Users/devzkn/code/cocoapodDemo/cocoapodStatic/KNAPP/KNCocoaTouchStaticLibrary
>```

######  [GitHub](https://github.com) 上创建一个公开项目，项目中必须包含这几个文件

- `LICENSE`:开源许可证
- `README.md`
- code
- `KNCocoaTouchStaticLibrary.podspec`:CocoaPods的描述文件**非常重要**



如下图：

![](https://ww2.sinaimg.cn/large/006tNbRwgy1fdfhvy3c19j31iq0dqn03.jpg)

`BYPhoneNumTF` 文件夹下是我存放代码的地方

`BYPhoneNumTF_Demo` 是代码使用样例（不是必须的）


#### 创建`.podspec` Create spec file stub.


>* `.podspec` 是用 Ruby 的配置文件，描述你项目的信息。
>```
> Specification
>```

>* cd仓库目录下 pod spec create
```
devzkndeMacBook-Pro:KNCocoaTouchStaticLibrary devzkn$ pod spec create KNCocoaTouchStaticLibrary
Specification created at KNCocoaTouchStaticLibrary.podspec
```	
><script src="https://gist.github.com/zhangkn/02cb3a7413b58c5db7c96797f0bd40b1.js"></script>



![](https://ww4.sinaimg.cn/large/006tNbRwgy1fdfioo1c4zj31bq0s20zn.jpg)

修改里面的配置就可以发布了~当然，没这么简单。

配置文件中的注释很多，而且很多配置都不是必须的，写多了等下验证还不让过~

so~**强烈建议**，直接拷贝下面的主要配置进行修改

```ruby
Pod::Spec.new do |s|
  s.name         = "BYPhoneNumTF" # 项目名称
  s.version      = "1.0.0"        # 版本号 与 你仓库的 标签号 对应
  s.license      = "MIT"          # 开源证书
  s.summary      = "A delightful TextField of PhoneNumber" # 项目简介

  s.homepage     = "https://github.com/qiubaiying/BYPhoneNumTF" # 你的主页
  s.source       = { :git => "https://github.com/qiubaiying/BYPhoneNumTF.git", :tag => "#{s.version}" }#你的仓库地址，不能用SSH地址
  s.source_files = "BYPhoneNumTF/*.{h,m}" # 你代码的位置， BYPhoneNumTF/*.{h,m} 表示 BYPhoneNumTF 文件夹下所有的.h和.m文件
  s.requires_arc = true # 是否启用ARC
  s.platform     = :ios, "7.0" #平台及支持的最低版本
  s.frameworks   = "UIKit", "Foundation" #支持的框架
  # s.dependency   = "AFNetworking" # 依赖库
  
  # User
  s.author             = { "BY" => "qiubaiyingios@163.com" } # 作者信息
  s.social_media_url   = "http://qiubaiying.github.io" # 个人主页

end
```
最最关键的步骤的到了，验证 `.podspec` 文件的格式是否正确，

	$ pod lib lint

验证会出现成功出现

	 -> BYPhoneNumTF (1.0.0)

	BYPhoneNumTF passed validation.	

但是很多情况没这么顺利，比如:

	 -> BYPhoneNumTF (1.0.0)
	    - WARN  | url: There was a problem validating the URL http://qiubaiying.github.io.
	
	[!] BYPhoneNumTF did not pass validation, due to 1 warning (but you can use `--allow-warnings` to ignore it) and all results apply only to public specs, but you can use `--private` to ignore them if linting the specification for a private pod.
	[!] The validator for Swift projects uses Swift 3.0 by default, if you are using a different version of swift you can use a `.swift-version` file to set the version for your Pod. For example to use Swift 2.3, run: 
	    `echo "2.3" > .swift-version`.
	You can use the `--no-clean` option to inspect any issue.
	
提示我们需要加`--allow-warnings`这么一句话，命令改为

	$ pod lib lint --allow-warnings

若还是提示什么`'echo "2.3" > .swift-version'`的，就加这么一个东西。

	$ echo "2.3" > .swift-version
然后在进行验证，这是应该就可以了。若还是不行，回到配置文件中检查有没有写错配置信息~

#### 给仓库打标签

验证成功后，将仓库提交到远程，然后给仓库打上标签并将标签也推送到远程。

标签相当于将你的仓库的一个压缩包，用于稳定存储当前版本。标签号与你在 `s.version = "1.0.0"`的版本号一致 `1.0.0`

	创建标签
	$ git tag -a 1.0.0 -m '标签说明' 
	推送到远程
	$ git push origin --tags
	
#### 发布`.podspec`

最后一步，发布项目的描述的文件 `BYPhoneNumTF.podspec` 

在仓库目录下执行
	
	pod trunk push BYPhoneNumTF.podspec
	
将你的 `BYPhoneNumTF.podspec` 发布到公有的speecs上,这一步其实做了很多操作,包括 

1. 更新本地 pods 库 `~/.cocoaPods.repo/master`
- 验证`.podspec`格式是否正确
- 将 `.podspec` 文件转成 JSON 格式
- 对 `master` 仓库 进行合并、提交.[master仓库地址](https://github.com/CocoaPods/Specs) 


成功后将会出现下列信息：

	Updating spec repo `master`
	Validating podspec
	 -> BYPhoneNumTF (1.0.0)
	
	Updating spec repo `master`
	
	--------------------------------------------------------------------------------
	 🎉  Congrats
	
	 🚀  BYPhoneNumTF (1.0.0) successfully published
	 📅  March 7th, 01:39
	 🌎  https://cocoapods.org/pods/BYPhoneNumTF
	 👍  Tell your friends!
	 
说明发布成功，你就可以通过上面的URL: <https://cocoapods.org/pods/BYPhoneNumTF> 进入的Pods查看自己的仓库信息了.

![](https://ww3.sinaimg.cn/large/006tNbRwgy1fded7yh8ugj31kw19djyk.jpg)

#### 使用仓库

发布到Cocoapods后，在终端更新本地pods仓库信息

	$ pod setup

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
>* [搭建一个提高开发效率的iOS静态库工程](https://blog.csdn.net/z929118967/article/details/73872024)
>```
>https://github.com/zhangkn/KNAPP/blob/master/README.md
>```
>* [截图反馈功能的实现](https://github.com/zhangkn/IOSStudy)
>```
> 图片的上传地址进行更换就可以应用到其他项目中（主要采用自定义View实现）。
```
