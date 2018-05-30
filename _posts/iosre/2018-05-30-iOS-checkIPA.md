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

plutil can be used to check the syntax of property list files, or convert a plist file from one format to another.  Specifying - as an input file reads from stdin.

#### Python  library

There are also many other ways to parse binary plist files. For example, I'm using Python and there is a library called` biplist`, which is exactly used to read and write binary plist files and support both Python 2 and 3. Also, the '`plistlib'` in Python 3.4 supports binary plist manipulation with new APIs.


# See Also 
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

