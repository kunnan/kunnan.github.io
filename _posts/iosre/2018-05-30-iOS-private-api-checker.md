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


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iOS-private-api-checker Developer tool to scan iOS apps for private API usage before submitting to Apple -t iosre
> #原来""的参数，需要自己加上""
```

