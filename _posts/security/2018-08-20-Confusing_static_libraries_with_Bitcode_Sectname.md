---
layout: post
title: Confusing_static_libraries_with_Bitcode_Sectname
date: 2018-08-20
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 混淆带有bitcode sectname 的静态库
---



# 前言



编译器混淆是基于中间代码（bitcode）进行的。那么编译生成一个带bitcode的静态库,我们就可以提取其中的bitcode进行混淆



> * 关于bitcode的注意事项
>   * 关闭bitcode 之后，此时的test 20-08-2018, 4.41 PM.xcarchive 是没有对应的dSYMs文件
>   * · Xcode 7默认开启Bitcode，如果应用开启Bitcode，那么其集成的其他第三方库也需要是Bitcode编译的包才能真正进行Bitcode编译 
>   * 开启Bitcode编译后，编译产生的.app体积会变大(中间代码，不是用户下载的包)，且.dSYM文件不能用来崩溃日志的符号化(用户下载的包是Apple服务重新编译产生的，有产生新的符号文件)
>     * 例子：[MachOView]([http://sourceforge.net/projects/machoview/](https://link.jianshu.com/?t=http://sourceforge.net/projects/machoview/)) `将“MachOView”拖到Application文件夹，就可以像其他程序一样启动了`[code](https://github.com/gdbinit/MachOView/releases/tag/Capstone)
>     * ![image](https://wx1.sinaimg.cn/large/af39b376gy1fuh1yqkhqyj20gt05zmxc.jpg)
>   * 通过Archive方式上传AppStore的包，可以在Xcode的Organizer工具中下载对应安装包的新的符号文件　　　
>     * ![image](https://wx3.sinaimg.cn/large/af39b376ly1fug9skns7zj20ty0fddh2.jpg)
>     * `/Users/devzkn/Library/Developer/Xcode/Archives`
>     * ![image](https://wx3.sinaimg.cn/large/af39b376ly1fug97469duj20lt088wf2.jpg)



# 将`__LLVM,__bitcode`的section里面的bitcode代码提取出来： ../libStaticLib.a -》result.bc

- __bitcode

  * `sectname __bitcode`

    ```
    Section
      sectname __bitcode
       segname __LLVM
          addr 0x0000000000000240
          size 0x0000000000001a30
        offset 2872
         align 2^4 (16)
        reloff 0
        nreloc 0
         flags 0x00000000
     reserved1 0
     reserved2 0
    
    ```

    ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufxtoacppj218n0k1dp0.jpg)



![image](https://ws2.sinaimg.cn/large/af39b376ly1fug8aycdp2j206s00pmx1.jpg)

#### 步骤

> * mkdir objects
>
> * **➜**  **~** cd objects
>
> * **➜**  **objects** ar -x ../libStaticLib.a
>
>   ```
>   ➜  objects tree -L 2
>   .
>   ├── StaticLib.o
>   └── __.SYMDEF\ SORTED
>   
>   0 directories, 2 files
>   
>   ```
>
> * **➜**  **objects** `segedit  ./StaticLib.o -extract "__LLVM" "__bitcode" result.bc`
>
>   ```
>   ➜  objects tree -L 2
>   .
>   ├── StaticLib.o
>   ├── __.SYMDEF\ SORTED
>   └── result.bc
>   
>   ```
>

# 用带混淆功能的clang重新编译bc 文件，生成目标文件:`result.bc->result.o->result.a`

 使用带混淆功能的clang将其重新编译成目标文件，生存的result.o 就是混淆后的object文件。将其重新打包成.a文件

 

> * **➜**  **objects** `/Users/devzkn/Library/Developer/Toolchains/Hikari.xctoolchain/usr/bin/clang -arch arm64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs -fobjc-arc -c result.bc -mllvm -enable-cffobf -mllvm -enable-bcfobf -o result.o`
>
>   ```
>   warning: ignoring debug info with an invalid version (700000003) in result.bc
>   warning: overriding the module target triple with arm64-apple-ios5.0.0 [-Woverride-module]
>   Failed To Loading Symbol Configuration From:/Users/devzkn/Hikari/SymbolConfig.json
>   Running BogusControlFlow On -[StaticLib myMethod:number:]
>   Running ControlFlowFlattening On -[StaticLib myMethod:number:]
>   Doing Post-Run Cleanup
>   Hikari Out
>   1 warning generated.
>   
>   ```
>
>   ```
>   ➜  objects tree -L 2
>   .
>   ├── StaticLib.o
>   ├── __.SYMDEF\ SORTED
>   ├── result.bc
>   └── result.o
>   
>   ```
>
> * **➜**  **objects** rm __.SYMDEF\ SORTED
>
>   ```
>   ➜  objects tree -L 2           
>   .
>   ├── StaticLib.o
>   ├── result.bc
>   └── result.o
>   
>   
>   ```
>
> * **➜**  **objects`**ar -crs result.a result.o`
>
>   ```
>   ➜  objects tree -L 2                
>   .
>   ├── StaticLib.o
>   ├── result.a
>   ├── result.bc
>   └── result.o
>   
>   ```
>
> * **➜**  **objects`**ranlib result.a`
>
>
>
>   > - 最后将混淆之后的静态库集成到app中。（此时的静态库没有了bitcode，因此整个主app也不能开启bitcode）
>   >   * ld: '/Users/devzkn/code/testCode/KNtestStaticLib/KNtestStaticLib/result.a(result.o)' does not contain bitcode. You must rebuild it with bitcode enabled (Xcode setting ENABLE_BITCODE), obtain an updated library from the vendor, or disable bitcode for this target. for architecture arm64
>   >   * ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufysix44oj20ct0baq4d.jpg)
>   >   * ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufypeoe9ij20od0a8t9m.jpg)

小结： 从静态库提出bitcode ，使用带有混淆功能的clang 重新编译bitcode进行混淆生成混淆之后的目标文件。并将目标文件进行打包成.a



#### 混淆之后的效果

![image](https://ws1.sinaimg.cn/large/af39b376gy1fufyylzjclj20m90ma0xa.jpg)



# 

# ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufz1japbnj21c20n7grs.jpg)



> * 混淆之前的样子
>
>   ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufz395atcj20zn0bfwgr.jpg)

# # Hikari目前支持下文所述的编译器标记



```
-enable-bcfobf 启用伪控制流  
-enable-cffobf 启用控制流平坦化
-enable-splitobf 启用基本块分割  
-enable-subobf 启用指令替换  
-enable-acdobf 启用反class-dump  
-enable-indibran 启用基于寄存器的相对跳转，配合其他加固可以彻底破坏IDA/Hopper的伪代码(俗称F5)  
-enable-strcry 启用字符串加密  
-enable-funcwra 启用函数封装
```

>  
>
> > - -enable-allobf一次性启用前文所述的所有标记
>
> >  
> >
> > > - 对于Xcode而言所有的混淆标记应该加在Target的Other C Flags中
> >
> > ```
> > 以clang为例，您需要在每个flag前加上-mllvm
> > 
> > ```



# demo code

> * https://github.com/ConfuseStaticLibraries/KNtestStaticLib
> * [StaticLib](https://github.com/ConfuseStaticLibraries/iOSREBook/tree/master/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/StaticLib)



#  使用Xcode混淆的方式，前提是要得到源码

![image](https://wx3.sinaimg.cn/large/af39b376ly1fug8p7x9y5j217i0j6dmg.jpg)
>* `-mllvm -enable-cffobf -mllvm -enable-bcfobf`

![image](https://wx3.sinaimg.cn/large/af39b376ly1fug9o2xu3zj20xa09xwfq.jpg)

![image](https://wx3.sinaimg.cn/large/af39b376ly1fug9ovzyt8j20vu091gmt.jpg)



> * 如果现在默认的编译器，就会报如下错误
>
>   ```
>   clang (LLVM option parsing): Unknown command line argument '-enable-cffobf'.  Try: 'clang (LLVM option parsing) -help'
>   clang (LLVM option parsing): Did you mean '-enable-ipra'?
>   clang (LLVM option parsing): Unknown command line argument '-enable-bcfobf'.  Try: 'clang (LLVM option parsing) -help'
>   clang (LLVM option parsing): Did you mean '-enable-ipra'?
>   Command /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang failed with exit code 1
>   
>   ```
>
>   * 因为此时使用的前端是/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang ，无法识别这个混淆标志；2⃣而带有混淆功能的clang是`/Users/devzkn/Library/Developer/Toolchains/Hikari.xctoolchain/usr/bin/clang`
>     * Hikari的[Releases](https://github.com/HikariObfuscator/Hikari/releases)有预先编译好的工具链，将Hikari.xctoolchain解压到~/Library/Developer/Toolchains/ 或/Library/Developer/Toolchains/ 即可(区别是前者只有当前用户可用，后者所有用户都可使用)
>



# See Also 



>* [Code_obfuscation](https://kunnan.github.io/2018/08/18/Code_obfuscation/)
>
>  * IR中间代码的生成：CodeGen 会负责将语法树自顶向下遍历逐步翻译成 LLVM IR，IR 是编译过程的前端的输出后端的输入`clang -emit-llvm -c main.m -o main.bc`
>
>    * 在Xcode中默认生成bitcode就是这种的中间形式存在， 开启了bitcode，那么苹果后台拿到的就是这种中间代码，苹果可以对bitcode做一个进一步的优化，如果有新的后端架构，仍然可以用这份bitcode去生成
>  * LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
>
>    ![image](https://ws1.sinaimg.cn/large/af39b376gy1fufx15k8dhj20sg0lc4jd.jpg)
>
>* [deeply-analyse-llvm/](https://ming1016.github.io/2017/03/01/deeply-analyse-llvm/)
>
>* [云栖社区](https://yq.aliyun.com/users/1788082404927770?spm=5176.11156381.0.0.3c866bceLTZLWD&do=login&accounttraceid=6665b2c3-6dfe-455e-9c2a-2ded296eeb83)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Confusing_static_libraries_with_Bitcode_Sectname 混淆带有bitcode sectname 的静态库 -t security
> #原来""的参数，需要自己加上""
```

