---
layout: post
title: iOS-private-api-checker
date: 2018-05-30
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Developer tool to scan iOS apps for private API usage before submitting to Apple
---


# [code 运行基础准备](https://github.com/iOSobfuscation/iOS-private-api-checker)


>* 安装依赖python包

```sh
pip install flask
pip install macholib
```

```
/usr/local/Cellar/python/3.6.5/libexec/bin/python: No module named macholib,如果没有安装解析私有库是不会成功的
```

>* 生成IOS项目SDK版本的私有api库

```py
sdks_config.append({
    'sdk': '10.3', 
    'framework': '/Applications/Xcode8.3.3.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator10.3.sdk/System/Library/Frameworks/', 
    'private_framework': '/Applications/Xcode8.3.3.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator10.3.sdk/System/Library/PrivateFrameworks/',
    'docset': '/Applications/Xcode8.3.3.app/Contents/Developer/Documentation/DocSets/com.apple.adc.documentation.docset/Contents/Resources/docSet.dsidx'
})
```

>* 开始重新生成api数据库

>

```
 /usr/bin/python  build_api_db.py
```

# 运行项目


#### 方式一： `open http://0.0.0.0:9527/`

>* 在根目录创建一个 tmp 目录，并设置 777 权限

```
mkdir tmp
sudo chmod -R 777 tmp/

运行build_api_db.py classdump 的头文件会保存在这里
├── tmp
│   ├── pri-headers
│   └── pub-headers
```


>* `/usr/bin/python  run_web.py`

```
 * Running on http://0.0.0.0:9527/ (Press CTRL+C to quit)
app /Users/devzkn/code/iosre/iOS-private-api-checker/tmp/20180530182916854360/Payload/kntmp
===============
private: 3
public 11
===============
===============
left app_varibles: 1193
app_methods: 431
private length: 19877
===============
===============
methods_in_app 0
methods_not_in_app 191
===============
```


#### （推荐）方式二：  检测检测并导出结果excel

>* 修改目录下的 iOS_private.py文件

修改 ipa_folder的值为你的ipa文件所在的目录的路径

>* 批量生成` /usr/bin/python iOS_private.py`

在tmp文件夹里可以找到生成好的xlsx文件



#  对原来的代码进行优化


####  修改`iOS-private-api-checker/dump/otool_utils.py` 使用jtool 来替代otool 

>* jtool - an alternative to otool 

```
jtool comes with a capability of running on Linux environment. Some ipa scanning tools are created to run on Linux environment where mac environment is not available.
```

>* 请暂时暂mac上运行，linux上暂时没有找到合适的、代替otool的工具，求推荐^^!-------推荐jtool 
>

```
otool_path = "otool" #otool所在的位置
otool_cmd = otool_path + " -L %s" # otool cmd模板字符串
```


```
To check if the executable is encrypted, run otool(jtool for linux)
```
[jtool](http://www.newosxbook.com/tools/jtool.html) 对otool 进行扩张

```
devzkndeMacBook-Pro:jtool  devzkn$ tree -L 4
.
├── Makefile
├── WhatsNew.txt
├── disarm
├── jtool
├── jtool.1
└── jtool.ELF64    支持linux64
```

>* [jtool.tar](http://www.newosxbook.com/tools/jtool.tar)
>

```
linux64:
	$(CC)  -DLINUX -DMACHLIB -D__DARWIN_UNIX03 -I./include -DLINUX  $(FILES) -o jtool.ELF64 -g2

linux32:
	$(CC) -m32 -DLINUX32 -DLINUX -DMACHLIB -D__DARWIN_UNIX03 -I./include -DLINUX  $(FILES) -o jtool.ELF32 -g2
```



# See Also 
>* [KNjtool](https://github.com/kunnan/KNjtool)
>* [iOS-private-api-checker](https://github.com/NetEaseGame/iOS-private-api-checker)

>* [blog.yahui.wang](https://blog.yahui.wang/2017/05/31/iOS-private-api-checker-tools/)
>* [iOS-OSX-Static-Analysis](https://github.com/secmobi/wiki.secmobi.com/blob/master/pages/tools/iOS-OSX-Static-Analysis.md)

>* [jtool的中文用法解释](https://bbs.pediy.com/thread-220100.htm)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iOS-private-api-checker Developer tool to scan iOS apps for private API usage before submitting to Apple -t iosre
> #原来""的参数，需要自己加上""
```

