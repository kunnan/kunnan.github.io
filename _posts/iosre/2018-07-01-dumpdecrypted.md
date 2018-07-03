---
layout: post
title: dumpdecrypted
date: 2018-07-01
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Dumps decrypted mach-o files from encrypted applications、framework or app extensions.
---



# 前言

本文重点推荐使用[frida-ios-dump-master](https://github.com/zhangkn/frida-ios-dump)，而非[dumpdecrypted.dylib](https://github.com/zhangkn/dumpdecrypted)。


# I 、frida-ios-dump-master 

使用frida-ios-dump-master 只需先用frida-ps查看applications Name ，之后执行dump.py 即可在dump.py 目录下生成砸壳之后的ipa包。

Frida环境的搭建可以看下[这篇文章](https://zhangkn.github.io/2017/12/frida/#gsc.tab=0)

#  II、[dumpdecrypted.dylib](https://github.com/stefanesser/dumpdecrypted)



#### 签名动态库文件



>*  列出可签名证书 `security find-identity -v -p codesigning `
>* 为dumpecrypted.dylib签名 `codesign --force --verify --verbose --sign "iPhone Developer: xxx xxxx (xxxxxxxxxx)" dumpdecrypted.dylib `

####  原理

>*dumpdecrypted的原理：通过向宏 DYLD_INSERT_LIBRARIES 里写入动态库的完整路径，就可以在可执行文件加载的时候，将动态链接库插入。

```
把自己通过DYLD_INSERT_LIBRARIES这个环境变量注入到已经通过系统加载器解密的 mach-o文件(因此要求程序是运行状态)，再把解密后的内存数据 dump出来--并没有破解 appstore的加密算法
```

>* [dumpdecrypted/dumpdecrypted.c](https://github.com/zhangkn/dumpdecrypted/blob/master/dumpdecrypted.c)
```
<!-- __attribute__((constructor)) 在main() 之前执行,__attribute__((destructor)) 在main()执行结束之后执行. -->
__attribute__((constructor))
void dumptofile(int argc, const char **argv, const char **envp, const char **apple, struct ProgramVars *pvars)
<!--  -->
```

砸壳的步骤：

>*1、找到app二进制文件对应的目录； 
```
ps -e|grep /var/mobile/Container*
```
>*2、找到app document对应的目录； 
```
cycript -p 
```
```
cy# [[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]
```
>*3、将砸壳工具dumpdecrypt.dylib拷贝到ducument目录下； //目的是为了获取写的权限 
```
devzkndeMacBook-Pro:dumpdecrypted-master devzkn$ scp ./dumpdecrypted.dylib root@192.168.2.212://var/mobile/Containers/Data/Application/91E7D6CF-A3D3-435B-849D-31BB53ED185B/Documents
```

>*4、砸壳；利用环境变量 DYLD_INSERT_LIBRARY 来添加动态库dumpdecrypted.dylib
```
DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/01ECB9D1-858D-4BC6-90CE-922942460859/KNWeChat.app/KNWeChat
```
第一个path为dylib，目标path 为app二进制文件对应的目录


>* iPhone:~ root# find / -name "*.*" | xargs grep "DYLD_INSERT_LIBRARIES" > ~/text.text
```
iPhone:~ root# cat  text.text
Binary file /Developer/Library/PrivateFrameworks/DTDDISupport.framework/libViewDebuggerSupport.dylib matches
Binary file /Developer/usr/lib/libBacktraceRecording.dylib matches
Binary file /Library/Frameworks/CydiaSubstrate.framework/Libraries/SubstrateLauncher.dylib matches
Binary file /Library/Frameworks/CydiaSubstrate.framework/Libraries/SubstrateLoader.dylib matches
Binary file /System/Library/Caches/com.apple.xpcd/xpcd_cache.dylib matches
/System/Library/LaunchDaemons/com.apple.searchd.plist:		<key>no_DYLD_INSERT_LIBRARIES</key>
```



# III、 [dumpdecrypted的改进版](https://github.com/kunnan/dumpdecrypted)

>* 以tweak的形式注入，避免以上繁琐的步骤

It is recommended to use [frida-ios-dump](https://github.com/AloneMonkey/frida-ios-dump) instead!

# IV、可以dump混编的class-dump

>* [可以dump混编的](https://github.com/zhangkn/KNBin/blob/master/swiftOCclass-dump)
```
devzkndeMBP:bin devzkn$ swiftOCclass-dump  --arch arm64 /Users/devzkn/decrypted/AppStoreV10.2/Payload/AppStore.app/AppStore -H -o  /Users/devzkn/decrypted/AppStoreV10.2/head
```

# V 、**Clutch**

通过`posix_spawnp`生成一个新的进程，然后暂停进程并dump内存。



# VI 架构不匹配的时候报：`mach-o, but wrong architecture`

![image](https://ws1.sinaimg.cn/large/af39b376gy1fsvv7terqtj20g306u3zb.jpg)

#### 解决方案

> * 使用[KNdumpdecryptedTweak](https://github.com/zhangkn/KNdumpdecryptedTweak)获取不同架构的二进制文件进行合并
>
> * 使用进行合并lipo -create 
>
>   ```
>   lipo -create ./WeChat /Users/devzkn/decrypted/wx6.7.0/WeChat.decrypted -output ./WeChat
>   ```
>
>   ```
>   ➜  WeChat.app  lipo -create Frameworks/TXLiteAVSDK_Smart_No_VOD.framework/TXLiteAVSDK_Smart_No_VOD /Users/devzkn/decrypted/wx6.7.0/arm64/TXLiteAVSDK_Smart_No_VOD.decrypted -output Frameworks/TXLiteAVSDK_Smart_No_VOD.framework/TXLiteAVSDK_Smart_No_VOD
>   ```
>
>   ```
>   lipo -create /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/WCDB.framework/WCDB /Users/devzkn/decrypted/wx6.7.0/arm64/WCDB.decrypted -output  /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/WCDB.framework/WCDB
>   ```
>
>   ```
>   lipo -create /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/QMapKit.framework/QMapKit /Users/devzkn/decrypted/wx6.7.0/arm64/QMapKit.decrypted -output /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/QMapKit.framework/QMapKit
>   ```
>
>   ```
>   lipo -create /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/MMCommon.framework/MMCommon /Users/devzkn/decrypted/wx6.7.0/arm64/MMCommon.decrypted -output /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/MMCommon.framework/MMCommon
>   ```
>
>   ```
>   lipo -create /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/MultiMedia.framework/MultiMedia /Users/devzkn/decrypted/wx6.7.0/arm64/MultiMedia.decrypted -output /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/MultiMedia.framework/MultiMedia
>   ```
>
>   ```
>   lipo -create /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/mars.framework/mars /Users/devzkn/decrypted/wx6.7.0/arm64/mars.decrypted -output /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Frameworks/mars.framework/mars
>   ```
>
>   系统库就不用合并了：
>
>   ```
>   /Users/devzkn/decrypted/wx6.7.0/Payload/WeChat.app/Watch/WeChatWatchNative.app/PlugIns/WeChatWatchNativeExtension.appex/Frameworks/libswiftCore.dylib 
>   ```
>
>   





# other 

####  签名

>* 查询可签名证书 
```
exit 0devzkndeMacBook-Pro:.git devzkn$ security find-identity -v -p codesigning
2) CB45FC98D2F6BC553EF706D835077 "iPhone Developer: kn zhang (48M9)"
17 valid identities found
```

>* 为dumpecrypted.dylib签名的例子
```
codesign --force --verify --verbose --sign "iPhone Developer: xxx xxxx (xxxxxxxxxx)" dumpdecrypted.dylib
```
# See Also 

>* [dumpdecrypted](https://zhangkn.github.io/2017/12/dumpdecrypted/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost dumpdecrypted Dumps decrypted mach-o files from encrypted applications、framework or app extensions. -t iosre
> #原来""的参数，需要自己加上""
```

