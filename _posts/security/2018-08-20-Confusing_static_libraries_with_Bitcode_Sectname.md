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



# 将`__LLVM,__bitcode`的section里面的bitcode代码提取出来

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



# 

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
> * rm __.SYMDEF\ SORTED
>
> * 

# 

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
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Confusing_static_libraries_with_Bitcode_Sectname 混淆带有bitcode sectname 的静态库 -t security
> #原来""的参数，需要自己加上""
```

