---
layout: post
title: Code_obfuscation
date: 2018-08-18
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 代码混淆
---

# 前言

>   



在编译器处理分析生成代码的时候，来 增加一些无用的代码、拆分代码块、使代码扁平化，进而提升静态分析的难度。

Chris Lattner 生于 1978 年，2005年加入苹果，将苹果使用的 GCC 全面转为 LLVM。2010年开始主导开发 Swift 语言。  

> * Xcode的编译器前端是clang，clang是llvm的一部分；而llvm本身是开源的，基于llvm 进行代码混淆的工具中，本人最终喜欢的马甲包混淆方案是用[`Hikari`](https://github.com/HikariObfuscator/Hikari),具体的用法看[这里](https://kunnan.github.io/2018/06/05/iosObfuscation/)
>   * Clang 是 LLVM 的子项目，是 C，C++ 和 Objective-C 编译器 http://clang.llvm.org/docs/
>     * 其中的 clang static analyzer 主要是进行语法分析，语义分析和生成中间代码，当然这个过程会对代码进行检查，出错的和需要警告的会标注出来。
>     * lld 是 Clang / LLVM 的内置链接器，clang 必须调用链接器来产生可执行文件。 

> *  编译器处理过程
>
>   * 1、预处理  
>
>     * 符号化 (Tokenization)  
>     * 宏定义的展开  
>     * #include 的展开  
>     * 语法和语义分析  
>
>   * 2、将符号化后的内容转化为一棵解析树 (parse tree)  
>
>     * 解析树做语义分析  
>     * 输出一棵抽象语法树（Abstract Syntax Tree* (AST)）  
>     * 生成代码和优化  
>
>   * 3、将 AST 转换为更低级的中间码 (LLVM IR)  
>
>     * 对生成的中间码做优化  
>       * LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
>     * 生成特定目标代码  
>     * 输出汇编代码  
>
>     
>
>   * 4、汇编器  
>
>     * 将汇编代码转换为目标对象文件。  
>
>   * 5、链接器  
>
>     * 将多个目标对象文件合并为一个可执行文件 (或者一个动态库)  





#### 1\编译的完整步骤

> * 1）编译信息写入辅助文件，创建文件架构 .app 文件  
> * 2）处理文件打包信息  
> * 3）执行 CocoaPod 编译前脚本，checkPods Manifest.lock  
> * 4）编译.m文件，使用 CompileC 和 clang 命令  
> * 5）链接需要的 Framework  
> * 6）编译 xib  
> * 7）拷贝 xib ，资源文件  
> * 8）编译 ImageAssets  
> * 9）处理 info.plist  
> * 10）执行 CocoaPod 脚本  
> * 11）拷贝标准库  
> * 12）创建 .app 文件和签名  



#### 2\clang 命令参数



> * -x 编译语言比如objective-c  
> * -arch 编译的架构，比如arm7  
> * -f 以-f开头的。  
> * -W 以-W开头的，可以通过这些定制编译警告  
> * -D 以-D开头的，指的是预编译宏，通过这些宏可以实现条件编译  
> * -iPhoneSimulator10.1.sdk 编译采用的iOS SDK版本  
> * -I 把编译信息写入指定的辅助文件  
> * -F 需要的Framework  
> * -c 标识符指明需要运行预处理器，语法分析，类型检查，LLVM生成优化以及汇编代码生成.o文件  
> * -o 编译结果  



#### 3\xcode 编译器的相关设置

> * Build Phases：构建可执行文件的规则 
>   * 1）指定 target 的依赖项目，在 target build 之前需要先 build 的依赖。在 Compile Source 中指定所有必须编译的文件，  
>   * 2）在 Link Binary With Libraries 里会列出所有的静态库和动态库，它们会和编译生成的目标文件进行链接。  
>   * 3）build phase 还会把静态资源拷贝到 bundle 里。  
>   * 4)可以通过在 build phases 里添加自定义脚本来做些事情，比如像 CocoaPods 所做的那样。  
> * Build Rules: 指定不同文件类型如何编译 
> *  Build Settings : 在 build 的过程中各个阶段的选项的设置



#### 4\Clang 使用的例子 : 从源码到可执行文件



![image](https://wx1.sinaimg.cn/large/af39b376gy1fudxjai910j207706eaar.jpg)

> * 1、 先通过-E查看clang在预编译处理这步做了什么: 包括宏的替换，头文件的导入 
>   * `clang -E main.m  `
> *  2、预处理完成后就会进行词法分析，这里会把代码切成一个个 Token，比如大小括号，等于号还有字符串等
> * 3、然后是语法分析，验证语法是否正确，然后将所有节点组成抽象语法树 AST 
> * 4、IR中间代码的生成：CodeGen 会负责将语法树自顶向下遍历逐步翻译成 LLVM IR，IR 是编译过程的前端的输出后端的输入 
>   * 1、Pass 教程： http://llvm.org/docs/WritingAnLLVMPass.html  
>   * 2、如果开启了 bitcode 苹果会做进一步的优化，有新的后端架构还是可以用这份优化过的 bitcode 去生成。  
>   * clang -emit-llvm -c main.m -o main.bc  
> *  5、生成汇编 
>   * clang -S -fobjc-arc main.m -o main.s  
> * 6、 生成目标文件
>   * clang -fmodules -c main.m -o main.o  
> * 7、生成可执行文件
>   * clang main.o -o main  

![image](https://wx1.sinaimg.cn/large/af39b376gy1fudwnnhg90j20yq0hvtee.jpg)

#### 5\Clang Static Analyzer静态代码分析： https://code.woboq.org/llvm/clang/



> * 静态分析前会对源代码分词成 Token，这个过程称为词法分析（Lexical Analysis）  
>
>   * `编译的概念（词法->语法->语义->IR->优化->CodeGen） 在 clang static analyzer 里到处可见。  `
>
> * Token 可以分为以下几类 
>
>   * 关键字：语法中的关键字，if else while for 等。  
>   * 标识符：变量名  
>   * 字面量：值，数字，字符串  
>   * 特殊符号：加减乘除等符号  
>
> * 语法分析（Semantic Analysis）将 token 先按照语法组合成语义生成 VarDecl 节点，然后将这些节点按照层级关系构成抽象语法树 Abstract Syntax Tree (AST)。
>
>   
>
>   





#### 6\CodeGen 生成 IR 代码 



> * 1）各种类，方法，成员变量等的结构体的生成，并将其放到对应的Mach-O的section中。  
> * 2）Non-Fragile ABI 合成 OBJCIVAR$_ 偏移值常量。  
> * 3）ObjCMessageExpr 翻译成相应版本的 objc_msgSend，super 翻译成 objc_msgSendSuper。  
> * 4）strong，weak，copy，atomic 合成 @property 自动实现 setter 和 getter。  
> * 5）@synthesize 的处理。  
> * 6）生成 block_layout 数据结构  
> * 7）block 和 weak  
> * 8）_block_invoke  
> * 9）ARC 处理，插入 objc_storeStrong 和 objc_storeWeak 等 ARC 代码。ObjCAutoreleasePoolStmt 转 objc_autorealeasePoolPush / Pop。自动添加 [super dealloc]。给每个 ivar 的类合成 .cxx_destructor 方法自动释放类的成员变量。  

#### 7\ IR 结构

```
LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
```

> * llc 编译器是专门编译 LLVM IR 的编译器用来生成汇编文件。  
> *  Driver
>   * Driver 是 Clang 面对用户的接口，用来解析 Option 设置，判断决定调用的工具链，最终完成整个编译过程。  
> *  Translate:Translate 就是把相关的参数对应到不同平台上不同的工具。
> * 每次编译后生成的 dSYM 文件 -
>   * dSYM 文件里存储了函数地址映射，这样调用栈里的地址可以通过 dSYM 这个映射表能够获得具体函数的位置。一般都会用来处理 crash 时获取到的调用栈 .crash 文件将其符号化  
>     * https://zhangkn.github.io/2018/03/symbolicatecrash/

# I \什么是LLVM？

llvm 是一系列   分模块和可重用的编译工具链，他提供了一种代码编写良好的中间表示（IR），可以作为多种语言的后端，还可以提供与编程无关的优化和针对多种CPU的代码生成功能。

> * LLVM架构的主要组成部分
>
>   * 前端：前端用来获取源代码然后将它转变为某种中间表示，我们可以选择不同的编译器来作为LLVM的前端，如gcc，clang。
>   * Pass(通常翻译为“流程”)：Pass用来将程序的中间表示之间相互变换。一般情况下，Pass可以用来优化代码，这部分通常是我们关注的部分。
>   * 后端：后端用来生成实际的机器码
>
> * [llvm 编译的整个流程介绍](http://www.alonemonkey.com/2016/12/21/learning-llvm/)
>
>   * The LLVM Project is a collection of modular and reusable compiler and toolchain technologies.
>
>   



#### 传统的编译器的架构如下

![image](https://wx1.sinaimg.cn/large/af39b376gy1fudx0dl8s6j20d60240sm.jpg)



#### LLVM的架构如下

![img](http://7xtdl4.com1.z0.glb.clouddn.com/script_1482136601642.png)



|                                                              |
| ------------------------------------------------------------ |
| <p>当编译器需要支持多种源代码和目标架构时，基于LLVM的架构，设计一门新的语言只需要去实现一个新的前端就行了，支持新的后端架构也只需要实现一个新的后端就行了。其它部分完成可以复用，就不用再重新设计一次了。</p> |
|                                                              |

 基于LLVM进行代码混淆时，只需关注中间层表示（IR）



# II \ 安装编译LLVM

#### 下载

> * 1.直接从官网下载:<http://releases.llvm.org/download.html>
>
> * 2.svn获取
>
>   * svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm
>
>   * ```
>     cd llvm/tools
>     svn co http://llvm.org/svn/llvm-project/cfe/trunk clang
>     cd ../projects
>     svn co http://llvm.org/svn/llvm-project/compiler-rt/trunk compiler-rt
>     cd ../tools/clang/tools
>     svn co http://llvm.org/svn/llvm-project/clang-tools-extra/trunk extra
>     
>     ```
>
>      
>
> * 3.git获取
>
>   ```
>   git clone http://llvm.org/git/llvm.git
>   cd llvm/tools
>   git clone http://llvm.org/git/clang.git
>   cd ../projects
>   git clone http://llvm.org/git/compiler-rt.git
>   cd ../tools/clang/tools
>   git clone http://llvm.org/git/clang-tools-extra.git
>   
>   ```



#### 编译



最新的LLVM只支持cmake来编译了，首先安装cmake。

```
brew install cmake

```



编译：

```
mkdir build
cmake /path/to/llvm/source
cmake --build .

```

> * 编译生成Xcode项目的命令
>
>   ```
>   mkdir build
>   cd build
>   cmake -G xcode CMAKE_BUILD_TYPE="Debug" ../llvm
>   open LLVM.xcodeproj
>   ```
>
>   * 打开Xcode的时候，选择`munually manage sechme`,选择需要编译的Target（clang），接下来执行`command+R`
>
> * 编译完成后，就能在`build/bin/`目录下面找到生成的工具了。
>
>   * clang 的版本和Xcode的版本不一样，苹果在开源的LLVM中增加了自己的修改

 



# See Also 

>* https://zhangkn.github.io/2018/03/Hopper/
>  * LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
>  * 当虚拟内存系统进行映射时，segment 和 section 会以不同的参数和权限被映射。  
>    * __TEXT segment 包含了被执行的代码。它被以只读和可执行的方式映射  
>    * __DATA segment 以可读写和不可执行的方式映射。它包含了将会被更改的数据。  
>    * __nl_symbol_ptr 和 __la_symbol_ptr，它们分别是 non-lazy 和 lazy 符号指针。  
>      * 延迟符号指针用于可执行文件中调用未定义的函数，例如不包含在可执行文件中的函数，它们将会延迟加载。而针对非延迟符号指针，当可执行文件被加载同时，也会被加载。  
>* [深入剖析 iOS 编译 Clang / LLVM 视频](https://segmentfault.com/l/1500000008514518)
>* [深入剖析 iOS 编译 Clang / LLVM 文章 Low Level Virtual Machine](https://ming1016.github.io/2017/03/01/deeply-analyse-llvm/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Code_obfuscation 代码混淆 -t security
> #原来""的参数，需要自己加上""
```

