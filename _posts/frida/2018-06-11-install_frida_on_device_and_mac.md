---
layout: post
title: install_frida_on_device_and_mac
date: 2018-06-11
tag: frida
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Frida的安装
---

# 前言



#### 1）frida 的基本命令操作



1、Frida是跨平台的注入工具，通过注入js于native的js引擎进行交互，从而执行native的代码进行hook和动态调用

- 使用frida-ps 查看app 信息

  - frida-ps -Uai


#### 2） Frida 工具

#### 2.1 基础的工具

> * 1、frida-ps -Uai
>
> * 2、使用Frida调试分析Windows、macOS、Linux、Android、iOS软件，现在时下再流行不过了。我常用它来提高逆向的效率
>
>   * 2.1https://zhangkn.github.io/2017/12/codeshare.frida.re/
>
>     * objc-method-observer
>       * frida --codeshare mrmacete/objc-method-observer -U -p 10490
>     * ios-app-info：使用-U -p 参数 查看app的信息
>       * frida --codeshare dki/ios-app-info -U -p 4929  
>
>   * 2.2 本文重点推荐使用[frida-ios-dump-master](https://github.com/zhangkn/frida-ios-dump)，而非dumpdecrypted.dylib
>
>     * [frida-ios-dump-master](https://github.com/AloneMonkey/frida-ios-dump) 就是在[dump-ios](https://codeshare.frida.re/@lichao890427/dump-ios/)基础之上进行改造的 
>
>     * 2.2.1 使用frida-ios-dump-master 只需先用frida-ps查看applications Name ，之后执行dump.py 即可在dump.py 目录下生成砸壳之后的ipa包。
>
>       ```
>       ➜  kunnan.github.io.git git:(master) ✗ cat ~/bin/kndump
>       #!/bin/sh
>       # iphone 的配置Start Cydia and add Frida’s repository by navigating to Manage -> Sources -> Edit -> Add and entering https://build.frida.re
>       # frida-ps -Uai 查看，来获取参数
>       # devzkndeMBP:bin devzkn$ frida-ps -Ua
>         # PID  Name       Identifier        
>       # -----  ---------  ------------------
>       # 14790  App Store  com.apple.AppStore
>       # usage: devzkndeMacBook-Pro:~ devzkn$ kndump 邮件
>       # ./dump.py 'App Store'
>       # dump app   
>       echo "" > ~/.ssh/known_hosts
>       cd ~/decrypted/frida-ios-dump-master 
>       rm -rf ./Payload
>       /usr/bin/python ./dump.py $1
>       open .
>       exit 0%  
>       ```
>
>





###### 2.2 dumpdecrypted



- 2.2.1[具体的操作步骤](https://zhangkn.github.io/2017/12/dumpdecrypted/)

  - 找到app二进制文件对应的目录

    - `ps -e|grep /var/mobile/Container*`

  - cypriot -p appname: 获取沙盒路径

    - cy# [NSHomeDirectory()] 

  - 将砸壳工具dumpdecrypt.dylib拷贝到ducument目录下； //目的是为了获取写的权限



    > ```
    > devzkndeMacBook-Pro:dumpdecrypted-master devzkn$ scp ./dumpdecrypted.dylib root@192.168.2.212://var/mobile/Containers/Data/Application/91E7D6CF-A3D3-435B-849D-31BB53ED185B/Documents
    > ```

  - 利用环境变量 DYLD_INSERT_LIBRARY 来添加动态库dumpdecrypted.dylib:

    > 第一个path为dylib，目标path 为app二进制文件对应的目录

    > 

    - `DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/01ECB9D1-858D-4BC6-90CE-922942460859/KNWeChat.app/KNWeChat`

- 2.2.2 dumpdecrypted的原理：通过向宏 DYLD_INSERT_LIBRARIES 里写入动态库的完整路径，就可以在可执行文件加载的时候，将动态链接库插入。

  - 

    > 把自己通过DYLD_INSERT_LIBRARIES这个环境变量注入到已经通过系统加载器解密的 mach-o文件(因此要求程序是运行状态)，再把解密后的内存数据 dump出来--并没有破解 appstore的加密算法

  - CydiaSubstrate.framework 本质也是使用环境变量

    - 使用find 命令查看即可验证这点

    ```
      find / -name “.” | xargs grep “DYLD_INSERT_LIBRARIES” > ~/text.text
    ```

    ```
      iPhone:~ root# cat  text.text
      Binary file /Developer/Library/PrivateFrameworks/DTDDISupport.framework/libViewDebuggerSupport.dylib matches
      Binary file /Developer/usr/lib/libBacktraceRecording.dylib matches
      Binary file /Library/Frameworks/CydiaSubstrate.framework/Libraries/SubstrateLauncher.dylib matches
      Binary file /Library/Frameworks/CydiaSubstrate.framework/Libraries/SubstrateLoader.dylib matches
      Binary file /System/Library/Caches/com.apple.xpcd/xpcd_cache.dylib matches
      /System/Library/LaunchDaemons/com.apple.searchd.plist:		<key>no_DYLD_INSERT_LIBRARIES</key>
    ```

      



###### 2.3 其他比较成熟的开源工具


>   * 3、[比较成熟的工具`pip3 install objection`:  objection - runtime mobile exploration](https://github.com/sensepost/objection)
>
>     * Dump the iOS keychain, and export it to a file.
>     * Dump data from common storage such as NSUserDefaults and the shared NSHTTPCookieStorage.
>     * Dump various formats of information in human readable forms.
>     * Watch for method executions by targeting all methods in a class, or just a single method.
>     * Dump encoded `.plist` files in a human readable format without relying on external parsers.
>
>     * Monitor the iOS pasteboard.
>
>      
>
>      
>
>      
>
>      
>
>      

 



#   I 、install frida-server through Cydia

为了使用Frida，需要在Mac和iOS上面分别安装Frida。

>* 1、 install frida-server through Cydia:Start Cydia and add Frida's repository by navigating to Manage -> Sources -> Edit -> Add and entering `https://build.frida.re`


```
-rwxr-xr-x 1 root wheel 11292672 Dec 14 00:54 /usr/sbin/frida-server*
-rw-r--r-- 1 root wheel 779 Dec 14 00:54 /Library/LaunchDaemons/re.frida.server.plist
```


>* 2、mac里面python自带easy_install pip
pip是python的包管理工具
```
devzkndeMacBook-Pro:site-packages devzkn$ sudo easy_install pip
```
>* 3、install the Frida Python package on your host machine
```
devzkndeMacBook-Pro:site-packages devzkn$ sudo -H pip install frida
```


>* 4、Connect your device via USB and make sure that Frida work
```
-U, --usb             connect to USB device
-a, --applications    list only applications
-i, --installed       include all installed applications
devzkndeMacBook-Pro:site-packages devzkn$  frida-ps -Uai
PID  Name          Identifier                 
---  ------------  ---------------------------
904  Cydia         com.saurik.Cydia           
856  微信            com.tencent.xin            
858  邮件            com.apple.mobilemail       
App Store     com.apple.AppStore         
```
>* 5、 upgrade frida
```
devzkndeMacBook-Pro:bin devzkn$ sudo pip install --upgrade frida --ignore-installed six
```



# II 、debug

动态调试py 脚本（dump.py）的例子

> * 1、pdb.py can be invoked as a script to debug other scripts.: ` python -m pdb  xxxxpy arg`
>
>   ```
>   python -m pdb  ./dump.py 微信
>   ```
>
>   * Pdb help
>
>     ```
>     (Pdb) h
>     Documented commands (type help <topic>):
>     ========================================
>     EOF    bt         cont      enable  jump  pp       run      unt   
>     a      c          continue  exit    l     q        s        until 
>     alias  cl         d         h       list  quit     step     up    
>     args   clear      debug     help    n     r        tbreak   w     
>     b      commands   disable   ignore  next  restart  u        whatis
>     break  condition  down      j       p     return   unalias  where 
>     ```
>



#### pdb 常用命令

 

 

- break 或b : 设置断点	设置断点

- continue或c: 继续执行程序

	 list 或l	: 查看当前行的代码段

	 step 或s	: 进入函数

- return 或r : 执行代码直到从当前函数返回

- exit 或 q : 中止并退出

	 next 或 n	: 执行下一行

	 pp	: 打印变量的值

  ```
  (Pdb) pp os.getcwd()
  '/Users/devzkn/Downloads/kevin\xef\xbc\x8dsoftware/ios-Reverse_Engineering/frida-ios-dump-master'
  ```

  * python print 汉字

    ```
    (Pdb) print sys.argv
    ['./dump.py', '\xe5\xbe\xae\xe4\xbf\xa1']
    (Pdb) print sys.argv[1]
    微信
    ```




#### Q&A

  

  

[具体请看这里](https://zhangkn.github.io/2017/12/frida/)

> * #### Failed to spawn 的替代方案
>
>   >  
>   >
>   > > - 1、先使用frida-ps -Uai 查看PID
>   >
>   > > - 2、使用 frida -p attach
>
> * -sh: /usr/sbin/frida-server: Bad CPU type in executable
>
>   ```
>   installed Frida for 32-bit devices
>   
>   ```
>
> * frida-server 没有启动
>
>   ```
>   iPhone:/usr/sbin root# killall SpringBoard
>   iPhone:/usr/sbin root# ps -e |grep frida-server
>    2290 ttys000    0:00.01 grep frida-server
>   ```
>

# See Also 



#### 签名

* 查询可签名证书

```
exit 0devzkndeMacBook-Pro:.git devzkn$ security find-identity -v -p codesigning
```



* 为dumpecrypted.dylib签名的例子

```
codesign --force --verify --verbose --sign "iPhone Developer: xxx xxxx (xxxxxxxxxx)" dumpdecrypted.dylib

```



#### [可以dump混编的](https://github.com/zhangkn/KNBin/blob/master/swiftOCclass-dump)

> * -A 可以查看方法在文件的实现地址

```
devzkndeMBP:bin devzkn$ swiftOCclass-dump  --arch arm64 /Users/devzkn/decrypted/AppStoreV10.2/Payload/AppStore.app/AppStore -H -o -A /Users/devzkn/decrypted/AppStoreV10.2/head

```



#### other 

>* [/www.frida.re/docs/ios/](https://www.frida.re/docs/ios/)
>
>  * Download the latest `FridaGadget.dylib` for iOS and sign it:
>
>    ```
>    $ mkdir Frameworks
>    $ cd Frameworks
>    $ frida_version=x.y.z
>    $ curl https://github.com/frida/frida/releases/download\
>    /$frida_version/frida-gadget-$frida_version-ios-univers\
>    al.dylib.xz | xz -d > FridaGadget.dylib
>    $ security find-identity -p codesigning -v
>      1) A30E15162B3EB979D2572783BF3… "Developer ID Application: …"
>      2) E18BA16DF86318F0ECA4BE17C03… "iPhone Developer: …"
>         2 valid identities found
>    $ codesign -f -s E18BA16DF86318F0ECA4BE17C03… FridaGadget.dylib
>    FridaGadget.dylib: replacing existing signature
>    
>    ```
>
>* [https://zhangkn.github.io/2017/12/codeshare.frida.re/](https://zhangkn.github.io/2017/12/codeshare.frida.re/)
>
>* Frida环境的搭建可以看下[这篇文章](https://zhangkn.github.io/2017/12/frida/#gsc.tab=0)
>
>* [dumpdecrypted](https://zhangkn.github.io/2017/12/dumpdecrypted/)
>
>  * 本文重点推荐使用[frida-ios-dump-master](https://github.com/zhangkn/frida-ios-dump)，而非dumpdecrypted.dylib。
>
>  * [Frida还能玩脱壳]( https://github.com/dstmath/frida-unpack)
>
>  * [A universal memory dumper using Frida](https://github.com/zhangkn/fridump)
>
>  * [Yet another frida based iOS dumpdecrypted](https://github.com/ChiChou/frida-ipa-dump)
>
>     
>
>* [使用Frida动态分析软件，你想要的姿势全部在这里](https://mp.weixin.qq.com/s/s_zR1Gq_MNj17vDVDuoDKg)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost install_frida_on_device_and_mac Frida的安装 -t frida
> #原来""的参数，需要自己加上""
```

