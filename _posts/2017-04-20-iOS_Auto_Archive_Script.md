---
layout:     post
title:      iOS_Auto_Archive_Script
subtitle:   利用xcdeobulid打包项目
date:       2017-04-20
author:    
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - iOS
    - Xcode
    - shell
    - ruby
---



# 前言


>* 利用xcode的命令行工具 `xcdeobulid` 进行项目的编译打包，生成ipa包.
>```
>xcodebuild -help
>```


# Xcodebuild

builds one or more targets contained in an Xcode project, or builds a scheme contained in an Xcode workspace or Xcode project

>*  `man xcodebuild`
>```
>     xcodebuild -workspace name.xcworkspace -scheme schemename [[-destination destinationspecifier] ...] [-destination-timeout value] [-configuration configurationname] [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]
     xcodebuild [-project name.xcodeproj] -scheme schemename [[-destination destinationspecifier] ...] [-destination-timeout value] [-configuration configurationname] [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]                
>```

>* Usage
>```
>1) To build an Xcode project
>2) To build an Xcode workspace
>3) display info about the installed version of Xcode or about projects or workspaces in the local directory:
>-list,
>-showBuildSettings, -showsdks, -usage,
>and -version.
>```
>

>* 	xcodebuild -list: Use with -project or -workspace
>```
>Information about project "KNBaseWebViewControllerDemo":
    Targets:
        KNBaseWebViewControllerDemo
    Build Configurations:
        Debug
        Release
This project contains no schemes.
>```
	
>* xcodebuild -workspace KNBaseWebViewControllerDemo.xcworkspace -list
>```
>Information about workspace "KNBaseWebViewControllerDemo":
    Schemes:
        KNBaseWebViewController
        KNBaseWebViewController-KNBaseWebViewController
        Pods-KNBaseWebViewControllerDemo
        KNBaseWebViewControllerDemo
>```	


# 自动打包流程

#### 1、 生成 archive

>*  `xcodebuild archive` 

	xcodebuild archive -workspace ${project_name}.xcworkspace \
                   -scheme ${scheme_name} \
                   -configuration ${build_configuration} \
                   -archivePath ${export_archive_path}

- 参数一：项目类型：混合项目 workspace 用 `-workspace`， project 用 `-project`

- `-scheme`：项目名

- `-configuration `：`Debug` 、 `Release`

- `-archivePath`：Specifies the path for the archive produced，精确到 `xx/xx.xcarchive`

首先需要创建一个`AdHocExportOptions.plist`文件


>* [knipa例子](https://github.com/zhangkn/KNBin/blob/master/knipa)
>```
>xcodebuild archive -workspace KNBaseWebViewControllerDemo.xcworkspace  -scheme KNBaseWebViewControllerDemo -configuration Debug -archivePath ~/KNBaseWebViewControllerDemo.archive
> ls -lrt  ~/
>KNBaseWebViewControllerDemo.archive.xcarchive
>```



#### 2、 [导出ipa包](https://github.com/zhangkn/KNBin/blob/master/knipa)

>*  xcodebuild -help
>```
>Available keys for -exportOptionsPlist:
>method: String 
> app-store, ad-hoc, package, enterprise, development, developer-id, and mac-application;Defaults to development.
>```


###### 2.1、-exportOptionsPlist

>* Available keys for -exportOptionsPlist:
>```
>	compileBitcode : Bool
>	embedOnDemandResourcesAssetPacksInBundle : Bool
>	iCloudContainerEnvironment : String
>	installerSigningCertificate : String
>	manifest : Dictionary
>method : String
>	onDemandResourcesAssetPacksBaseURL : String
>	provisioningProfiles : Dictionary	 For manual signing only
>	signingCertificate : String 	For manual signing only
>	signingStyle : String
>	stripSwiftSymbols : Bool
>	teamID : String
>	thinning : String For non-App Store exports
>	uploadBitcode : Bool  For App Store exports
>	uploadSymbols : Bool 	For App Store exports 如果为NO，将减小ipa包，但不利于问题定位；因此通常分别打一个。
>```


#### 2.2、-exportArchive

>*  `xcodebuild -exportArchive -archivePath xcarchivepath -exportPath destinationpath -exportOptionsPlist path`


	xcodebuild  -exportArchive \
	            -archivePath ${export_archive_path} \
	            -exportPath ${export_ipa_path} \
	            -exportOptionsPlist ${ExportOptionsPlistPath}


- `xcarchivepath `：xcodebuild archive 生成的文件路径
- `destinationpath`：ipa包的路径
- `path`：导出ipa包类型，配置文件路径；Available keys for -exportOptionsPlist: 使用 `xcodebuild -help`查看 ;其中method key 的默认值：Defaults to development.




# 上传到Fir

将项目上传到 [Fir](https://fir.im)

下载 [fir 命令行工具](https://github.com/FIRHQ/fir-cli/blob/master/doc/install.md) 

	gem install fir-cli

获取 fir 的 API Token（右上角）

![](https://ww3.sinaimg.cn/large/006tNc79gy1ff28ccsqhyj304t07bwei.jpg)

上传

	fir publish "ipa_Path" -T "firApiToken"
	


# [自动打包脚本](https://github.com/zhangkn/KNBin/blob/master/knipa)


>* knxcodeipa
><script src="https://gist.github.com/zhangkn/0f37ea59af8569a31257f26cc1cfee1e.js"></script>
>
>* KNBaseWebViewControllerDemo-v1.0.ipa
>```
>devzkndeMBP:KNBaseWebViewControllerDemo-IPA devzkn$ tree -L 1
.
├── DistributionSummary.plist
├── ExportOptions.plist
├── KNBaseWebViewControllerDemo-v1.0.ipa
├── KNBaseWebViewControllerDemo.xcarchive
└── Packaging.log
>```
>
>* [完成打包代码](https://github.com/zhangkn/KNBin/blob/master/knipa)


>* 附件：developmentExportOptionsPlist
><script src="https://gist.github.com/zhangkn/60125fb7f453953286e041713a757020.js"></script>


# see also

>* [iOSAutoArchiveScript](https://github.com/qiubaiying/iOSAutoArchiveScript)

>* [ Xcode Builds Settings Reference](https://help.apple.com/xcode/mac/current/#/itcaec37c2a6)

>* xcrun
>```
> ibtool(1), sysexits(3), xcode-select(1), xcrun(1), xed(1)
>```
