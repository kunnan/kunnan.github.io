---
layout: post
title: iOS-checkIPA
date: 2018-05-30
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Scans an IPA file and parses its Info.plist and embedded.mobileprovision files. Performs checks of expected key/value relationships and displays the results.
---


# Contents in an IPA

>* Payload - Contains the .app folder of the specific iOS application. Under the .app folder we can see the application’s contents like the images, nib files which store the user interface and so on.
>* Mach-O Executable - Mach Object files are file formats for executables.
Contains data section, header and load commands.
>* Info.plist - Stores the configuration information of the executable. Can be
viewed with a text editor. If it is in a binary format, can be converted using
```
plutil -convert xml1 Info.plist
```
>* Frameworks - Folder with libraries the application uses. There are many
 third party libraries. For example the AWS SDK.
>* Mobileprovision - Information such as the developer certificate, devices
 for which the application is provisioned or team identifier can be found
 under embedded.mobileprovision


#### 分析步骤

IPA files downloaded from the iOS App Store are encrypted by default with Apple’s DRM, [Fairplay](https://en.wikipedia.org/wiki/FairPlay). To get around the encryption, one can use[ clutch](https://github.com/KJCracks/Clutch) or [ios-dump](https://github.com/AloneMonkey/frida-ios-dump).


>*  To check if the executable is encrypted, run otool(jtool for linux).
```
 otool -l ~/kntmp | grep -i crypt
```

If the value of cryptid is 1, it implies that the binary is encrypted. On
decrypting, the value would be 0.

#### 手动分析 

###### Domain Names

>*  inside the directory using `grep` or running `strings` on the executable.
>

```
strings ~/kntmp | egrep -i 'http|https'
```

```
 cat Info.plist | grep NSExceptionDomains -A13
```


# KNiOS-checkIPA

>* [/Users/devzkn/code/iosre/iOS-checkIPA](https://github.com/kunnan/KNiOS-checkIPA)

```sh
 /usr/bin/python checkipa -i /Users/devzkn/Desktop/Payload.ipa 
```

```
Distribution: code signing Entitlements 'get-task-allow' value is set to YES; should be NO
```

# 知识补充

>* 将app 转化为ipa

```
打包： Payload文件夹放.app 文件；压缩Payload,修改后缀名为.ipa;
```


####  PlistBuddy [-cxh] file.plist


```
/usr/libexec/PlistBuddy -c "Print :CFBundleIdentifier" yourBinaryOrXmlPlist.plist
```


####  plutil [command_option] [other_options] file

plutil can be used to check the syntax of property list files, or convert a plist file from one format to another.  Specifying - as an input file reads from stdin. plutil 使plist文件在二进制和XML之间转换


>* 将二进制转为xml
```
plutil -convert xml1 Info.plist
```

```sh
plutil -convert binary1 -o binary.plist xml.plist //将xml转为二进制
```

#### Python  library

There are also many other ways to parse binary plist files. For example, I'm using Python and there is a library called` biplist`, which is exactly used to read and write binary plist files and support both Python 2 and 3. Also, the '`plistlib'` in Python 3.4 supports binary plist manipulation with new APIs.


# See Also 

>* [iOS-private-api-checker-tools](https://blog.yahui.wang/2017/05/31/iOS-private-api-checker-tools/)
>


>* [Web path scanner](https://github.com/maurosoria/dirsearch)

>* [A python script that finds endpoints in JavaScript files ](https://github.com/GerbenJavado/LinkFinder)
>* [ios-static-analysis-and-recon](https://secdevops.ai/ios-static-analysis-and-recon-c611eaa6d108)
>* [wiki.secmobi.com](https://github.com/iOSHacking/wiki.secmobi.com)
>* [A Python script using construct (http://construct.wikispaces.com/) to parse the code directory in a Mach-O file.  In the future it might parse the whole Mach-O that way.
](https://github.com/comex/cs)

>* [iOS/OSX Static Analysis](https://github.com/secmobi/wiki.secmobi.com/blob/master/pages/tools/iOS-OSX-Static-Analysis.md)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iOS-checkIPA Scans an IPA file and parses its Info.plist and embedded.mobileprovision files. Performs checks of expected key/value relationships and displays the results. -t iosre
> #原来""的参数，需要自己加上""
```

