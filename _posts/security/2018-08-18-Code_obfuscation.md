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

> * LLVM编译一个源文件的过程：`预处理 -> 词法分析 -> Token -> 语法分析 -> AST -> 代码生成 -> LLVM IR -> 优化 -> 生成汇编代码 -> Link -> 目标文件`
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
>   * 4、汇编器  
>
>     * 将汇编代码转换为目标对象文件。  
>
>   * 5、链接器  
>
>     * 将多个目标对象文件合并为一个可执行文件 (或者一个动态库)  



# 可以用Clang做什么？



> * [libclang进行语法分析](https://github.com/zhangkn/KNParseClangLib/blob/master/ParseClangLib/main.m)
>
>   * [可以使用libclang里面提供的方法对源文件进行语法分析，分析它的语法树，遍历语法树上面的每一个节点。可以用于检查拼写错误，或者做字符串加密。](https://github.com/zhangkn/KNParseClangLib)
>     * https://kunnan.github.io/2018/08/17/String_encryption/
>
> * LibTooling: 
>
>   * 我们也可以自己写一个这样的工具去遍历、访问、甚至修改语法树。 目录:`llvm/tools/clang/tools`
>
>     ```
>     #include "clang/Driver/Options.h"
>     #include "clang/AST/AST.h"
>     #include "clang/AST/ASTContext.h"
>     #include "clang/AST/ASTConsumer.h"
>     #include "clang/AST/RecursiveASTVisitor.h"
>     #include "clang/Frontend/ASTConsumers.h"
>     #include "clang/Frontend/FrontendActions.h"
>     #include "clang/Frontend/CompilerInstance.h"
>     #include "clang/Tooling/CommonOptionsParser.h"
>     #include "clang/Tooling/Tooling.h"
>     #include "clang/Rewrite/Core/Rewriter.h"
>     using namespace std;
>     using namespace clang;
>     using namespace clang::driver;
>     using namespace clang::tooling;
>     using namespace llvm;
>     Rewriter rewriter;
>     int numFunctions = 0;
>     static llvm::cl::OptionCategory StatSampleCategory("Stat Sample");
>     class ExampleVisitor : public RecursiveASTVisitor<ExampleVisitor> {
>     private:
>         ASTContext *astContext; // used for getting additional AST info
>     public:
>         explicit ExampleVisitor(CompilerInstance *CI) 
>           : astContext(&(CI->getASTContext())) // initialize private members
>         {
>             rewriter.setSourceMgr(astContext->getSourceManager(), astContext->getLangOpts());
>         }
>         virtual bool VisitFunctionDecl(FunctionDecl *func) {
>             numFunctions++;
>             string funcName = func->getNameInfo().getName().getAsString();
>             if (funcName == "do_math") {
>                 rewriter.ReplaceText(func->getLocation(), funcName.length(), "add5");
>                 errs() << "** Rewrote function def: " << funcName << "\n";
>             }    
>             return true;
>         }
>         virtual bool VisitStmt(Stmt *st) {
>             if (ReturnStmt *ret = dyn_cast<ReturnStmt>(st)) {
>                 rewriter.ReplaceText(ret->getRetValue()->getLocStart(), 6, "val");
>                 errs() << "** Rewrote ReturnStmt\n";
>             }        
>             if (CallExpr *call = dyn_cast<CallExpr>(st)) {
>                 rewriter.ReplaceText(call->getLocStart(), 7, "add5");
>                 errs() << "** Rewrote function call\n";
>             }
>             return true;
>         }
>     };
>     class ExampleASTConsumer : public ASTConsumer {
>     private:
>         ExampleVisitor *visitor; // doesn't have to be private
>     public:
>         // override the constructor in order to pass CI
>         explicit ExampleASTConsumer(CompilerInstance *CI)
>             : visitor(new ExampleVisitor(CI)) // initialize the visitor
>         { }
>         // override this to call our ExampleVisitor on the entire source file
>         virtual void HandleTranslationUnit(ASTContext &Context) {
>             visitor->TraverseDecl(Context.getTranslationUnitDecl());
>         }
>     };
>     class ExampleFrontendAction : public ASTFrontendAction {
>     public:
>         virtual std::unique_ptr<ASTConsumer> CreateASTConsumer(CompilerInstance &CI, StringRef file) {
>              return llvm::make_unique<ExampleASTConsumer>(&CI); // pass CI pointer to ASTConsumer
>         }
>     };
>     int main(int argc, const char **argv) {
>         // parse the command-line args passed to your code
>         CommonOptionsParser op(argc, argv, StatSampleCategory);        
>         // create a new Clang Tool instance (a LibTooling environment)
>         ClangTool Tool(op.getCompilations(), op.getSourcePathList());
>         // run the Clang Tool, creating a new FrontendAction (explained below)
>         int result = Tool.run(newFrontendActionFactory<ExampleFrontendAction>().get());
>         errs() << "\nFound " << numFunctions << " functions.\n\n";
>         // print out the rewritten source code ("rewriter" is a global var.)
>         rewriter.getEditBuffer(rewriter.getSourceMgr().getMainFileID()).write(errs());
>         return result;
>     }
>     
>     ```
>
>     * 上面的代码通过遍历语法树，去修改里面的方法名和返回变量名：
>
>       ```
>       before:
>       void do_math(int *x) {
>           *x += 5;
>       }
>       int main(void) {
>           int result = -1, val = 4;
>           do_math(&val);
>           return result;
>       }
>       after:
>       ** Rewrote function def: do_math
>       ** Rewrote function call
>       ** Rewrote ReturnStmt
>       Found 2 functions.
>       void add5(int *x) {
>           *x += 5;
>       }
>       int main(void) {
>           int result = -1, val = 4;
>           add5(&val);
>           return val;
>       }
>       
>       ```
>
>       * 那么，我们看到`LibTooling`对代码的语法树有完全的控制，那么我们可以基于它去检查命名的规范，甚至做一个代码的转换，比如实现OC转Swift。
>
>          
>
>   * 对语法树有完全的控制权，可以作为一个单独的命令使用，如：`clang-format`
>
>     ```
>     	
>     clang-format main.m
>     
>     ```
>
> * ClangPlugin: `对语法树有完全的控制权，作为插件注入到编译流程中，可以影响build和决定编译过程。目录:`llvm/tools/clang/examples
>
>   * 可以用来定义一些编码规范，比如代码风格检查，命名检查等等
>



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

> * 1、 先通过-E查看clang在预编译处理这步做了什么: 包括宏的替换，macro宏的展开  import/include头文件的导入，以及#if等处理
>
>
>   * `clang -E main.m  `
>
>   * 后来Xcode新建的项目里面去掉了pch文件，引入了moduels的概念，把一些通用的库打成modules的形式，然后导入，默认会加上-fmodules参数。
>
>     * clang -E -fmodules main.m 
>
>       * 这样的话，只需要@import一下就能导入对应库的modules模块了。  
>
>         ```
>         @import Foundation; 
>         
>         ```
>
> * 2、预处理完成后就会进行词法分析，这里会把代码切成一个个 Token，比如大小括号，等于号还有字符串等
>
>   * clang -fmodules -fsyntax-only -Xclang -dump-tokens main.m 
>
> * 3、然后是语法分析，验证语法是否正确，然后将所有节点组成抽象语法树 AST 
>
>   * clang -fmodules -fsyntax-only -Xclang -ast-dump main.m 
>
> * 4、IR中间代码的生成：CodeGen 会负责将语法树自顶向下遍历逐步翻译成 LLVM IR，IR 是编译过程的前端的输出后端的输入 
>   * 可以在中间代码层次去做一些优化工作，我们在Xcode的编译设置里面也可以设置优化级别`-O1`,`-O3`,`-Os`。 还可以去写一些自己的Pass，这里需要解释一下什么是Pass。
>
>      
>
>   * 1、Pass 教程： http://llvm.org/docs/WritingAnLLVMPass.html  
>
>     * Pass就是LLVM系统转化和优化的工作的一个节点，每个节点做一些工作，这些工作加起来就构成了LLVM整个系统的优化和转化。
>
>   
>
>   * 2、如果开启了 bitcode 苹果会做进一步的优化，有新的后端架构还是可以用这份优化过的 bitcode 去生成。  
>     *  生成字节码 (LLVM Bitcode)
>       * 在Xcode7中默认生成bitcode就是这种的中间形式存在， 开启了bitcode，那么苹果后台拿到的就是这种中间代码，苹果可以对bitcode做一个进一步的优化，如果有新的后端架构，仍然可以用这份bitcode去生成
>     * clang -emit-llvm -c main.m -o main.bc  
>
> * 5、生成汇编 
>
>   * clang -S -fobjc-arc main.m -o main.s  
>
> * 6、 生成目标文件
>
>   * clang -fmodules -c main.m -o main.o  
>
> * 7、生成可执行文件
>
>   * clang main.o -o main  

![image](https://wx1.sinaimg.cn/large/af39b376gy1fudwnnhg90j20yq0hvtee.jpg)

> 8\整体的流程
>
> ![image](https://wx1.sinaimg.cn/large/af39b376gy1fudxkw5w6bj20tm02aq2t.jpg)



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



#### 传统的编译器的架构如下

![image](https://wx1.sinaimg.cn/large/af39b376gy1fudx0dl8s6j20d60240sm.jpg)



#### LLVM的架构如下

![img](http://7xtdl4.com1.z0.glb.clouddn.com/script_1482136601642.png)



|                                                              |
| ------------------------------------------------------------ |
| <p>当编译器需要支持多种源代码和目标架构时，基于LLVM的架构，设计一门新的语言只需要去实现一个新的前端就行了，支持新的后端架构也只需要实现一个新的后端就行了。其它部分完成可以复用，就不用再重新设计一次了。</p> |
|                                                              |

 基于LLVM进行代码混淆时，只需关注中间层表示（IR）

#### 基于LLVM，我们可以做什么？



> * 做语法树分析，实现语言转换OC转Swift、JS or 其它语言，字符串加密。
> * 编写ClangPlugin，命名规范，代码规范，扩展功能。
> * 编写Pass，代码混淆优化。
>
>  
>
>  
>
>  

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

 





# III 、**PassDemo**



> * [pass 处理编译过程中代码的额转换以及优化工作。所有的pass都是pass的子类，不同的passs实现不同的作用，可以分别继承不同的pass类实现对应的功能](https://github.com/TrustedCloud/private-llvm/blob/7c6e1aa9a4b94213e24e647641dd39c7418e05a1/tools/opt/PassPrinters.h#L4)
>   * ModulePass
>   * *CallGraphSCCPass*
>   * FunctionPass
>   * BasicBlockPass
>   * RegionPass
>   * LoopPass





> * 接下来重点介绍如何编写、编译、加载、运行一个pass
>
>   * [官方的demo在`lib/Transfroms/hello` 中](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/PassDemo/Hello.cpp)
>
>     ![image](https://wx1.sinaimg.cn/large/af39b376gy1fudz7ut679j20yz0k9afg.jpg)



#### 配置编译环境

> * 在lib/Transforms/新建一个文件夹 Hello 
>
> * 配置编译脚本去编译源代码
>
>   * [CMakeLists.txt](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/PassDemo/CMakeLists.txt)
>
>     ```
>     add_llvm_loadable_module( LLVMHello
>       Hello.cpp
>     
>       DEPENDS
>       intrinsics_gen
>       PLUGIN_TOOL
>       opt
>       )
>     
>     ```
>
>     * 修改`.../Transforms/CMakeLists.txt`, 加上刚刚写的模块`hello`
>
>       ```
>       add_subdirectory(hello)
>       
>       ```
>
>     * 编译脚本指定使用编译源文件 Hello.cpp 生成  $(LEVEL)/lib/LLVMHello.dylib 动态库，该文件可以被opt 通过-load 参数动态加载。
>
>       ![image](https://wx3.sinaimg.cn/large/af39b376gy1fuerby37ccj20lj0efn0o.jpg)
>
> * open Xcode 重新build，在项目里面添加LLVMHello 的target为secheme；在过程中找到 loadable modules 下面LLVMHello 里面的hello.cpp ，开始编写代码。



#### 编写代码



![image](https://wx3.sinaimg.cn/large/af39b376gy1fueri4vamvj223w1kw462.jpg)



> * 编写一个pass操作function，输出一些相关信息，需要使用如下头文件
>
>   * `#include "llvm/IR/Function.h" `
>
>   * `#include "llvm/Pass.h" `
>
>   * `#include "llvm/Support/raw_ostream.h" `
>
>     * 这些头文件里面的方法属于LLVM命名空间，需要声明使用LLVM命名空间
>
>       ```
>       using namespace llvm;
>       
>       ```
>
> * 编写一个匿名空间；C++的匿名空间和c的static 关键词一样，定义在匿名空间中的变量仅对当前文件可见。
>
>   * code
>
>     ```
>     namespace {
>       // Hello - The first implementation, without getAnalysisUsage.
>       struct Hello : public FunctionPass {// 定义hello类继承自FunctionPass；功能不同的pass 会继承对应的父类
>         static char ID; // Pass identification, replacement for typeid 定义可供LLVM 标示的passID
>         Hello() : FunctionPass(ID) {}
>     
>         bool runOnFunction(Function &F) override {//定义一个runOnFunction 重载继承父类的抽象虚函数，这个函数里面可以针对函数进行特殊处理（打印每个方法的名字）。
>           ++HelloCounter;
>           errs() << "Hello: ";
>           errs().write_escaped(F.getName()) << '\n';
>           return false;
>         }
>       };
>     }
>     
>     ```
>
> * 初始化passID
>
>   * code: 因为LLVM将使用ID的地址去识别一个pass，因此初始化的value不重要
>
>     ```
>     char Hello::ID = 0;
>     
>     ```
>
> * 注册pass hello，指定命令行参数为“hello”，名字说明为“hello world pass” 
>
>   * code
>
>     ```
>     static RegisterPass<Hello> X("hello", "Hello World Pass");
>     
>     ```
>
> * 选中target LLVMhello 执行command+B，生成build/debug/lib/LLVMHello.dylib 
>
> * hello2 : `Hello World Pass (with getAnalysisUsage implemented) `
>
>   * code 
>
>     ```
>     namespace {
>       // Hello2 - The second implementation with getAnalysisUsage implemented.
>       struct Hello2 : public FunctionPass {
>         static char ID; // Pass identification, replacement for typeid
>         Hello2() : FunctionPass(ID) {}
>     
>         bool runOnFunction(Function &F) override {
>           ++HelloCounter;
>           errs() << "Hello: ";
>           errs().write_escaped(F.getName()) << '\n';
>           return false;
>         }
>     
>         // We don't modify the program, so we preserve all analyses.
>         void getAnalysisUsage(AnalysisUsage &AU) const override {
>           AU.setPreservesAll();
>         }
>       };
>     }
>     
>     char Hello2::ID = 0;
>     static RegisterPass<Hello2>
>     Y("hello2", "Hello World Pass (with getAnalysisUsage implemented)");
>     
>     ```
>
> * [hello1 code](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/PassDemo/Hello.cpp)
>





#### 使用opt  加载动态库，并指定参数

之前在代码中通过RegisterPass 注册了pass，现在可以准备一个测试用的源文件，通过opt -load 命令去加载动态库并指定参数hello



> * 测试的源文件[test.mm](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/PassDemo/test.mm)
>
>   * code
>
>     ```
>     #include <stdio.h>
>     int add(int x, int y) {
>         return x + y;
>     }
>     int main(){
>         printf("%d",add(3,4));
>         return 0;
>     }
>     
>     ```
>
> * 编译源文件 生成bitcode: 因为Pass是作用于中间代码，所以我们首先要生成一份中间代码
>
>   * path/to/build/deBug/bin/clang -emit-llvm -c test.mm -o test.bc
>
> * 然后加载Pass
>
>   * 在Xcode中将opt的target添加到scheme中。编辑scheme的启动参数，按command+R执行。
>   * ../build/bin/opt -load ../build/lib/LLVMHello.dylib -simplepass < test.bc > after_test.bc 
>
> * 通过-help   参数查看注册的pass参数
>
>   ```
>   ./opt -load ../lib/LLVMHello.dylib -help
>   ```
>
> * passManager 提供了`-time-passes`参数用于输出pass的时间占比
>
>   ```
>   ./opt -load ../lib/LLVMHello.dylib -hello -time-passes -disable-output ~/LLVM/test.bc
>   ```
>
> * 编辑/llvm-3.9.0.src/tools/opt/CMakeLists.txt 将LLVMHello模块添加到opt 的依赖，这样在执行opt的时候能自动检测LLVMHello模块有没有被修改，如果被修改了。需要重新编译LLVMHello模块
>
>   ```
>   add_llvm_tool(opt
>     AnalysisWrappers.cpp
>     BreakpointPrinter.cpp
>     GraphPrinters.cpp
>     NewPMDriver.cpp
>     PassPrinters.cpp
>     PrintSCC.cpp
>     opt.cpp
>   
>   
>   DEPENDS
>   LLVMHello
>     )
>   ```
>
>
> 

# 其他操作



#### 1、如果编写的pass用到了其他pass提供的函数功能，需要在`getAnalysisUsage ` 中进行声明

[Hello.cpp](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/PassDemo/Hello.cpp)

> * 例如想获取程序中存在的循环信息
>
>   * 导入头文件llvm/Analysis/LoopInfo.h,并在`getAnalysisUsage ` 中进行调用
>
>   * code 
>
>     ```
>     namespace {
>       // Hello2 - The second implementation with getAnalysisUsage implemented.
>       struct Hello2 : public FunctionPass {
>         static char ID; // Pass identification, replacement for typeid
>         Hello2() : FunctionPass(ID) {}
>     
>         bool runOnFunction(Function &F) override {
>           ++HelloCounter;
>           errs() << "Hello: ";
>           errs().write_escaped(F.getName()) << '\n';
>           return false;
>         }
>     
>         // We don't modify the program, so we preserve all analyses.
>         void getAnalysisUsage(AnalysisUsage &AU) const override {
>         AU.addRequired<LoopInfoWrapperPass>();
>           AU.setPreservesAll();
>         }
>       };
>     }
>     
>     char Hello2::ID = 0;
>     static RegisterPass<Hello2>
>     Y("hello2", "Hello World Pass (with getAnalysisUsage implemented)");
>     
>     ```
>
>     * 通过它提供的接口就可以获取循环的个数
>
>       ```
>       #include "llvm/Pass.h"
>       #include "llvm/IR/Function.h"
>       #include "llvm/Support/raw_ostream.h"
>       #include "llvm/Analysis/LoopInfo.h"
>       
>       using namespace llvm;
>       
>       namespace{
>           struct Hello : public FunctionPass{
>               static char ID;
>               
>               Hello() : FunctionPass(ID){}
>               
>               virtual void getAnalysisUsage(AnalysisUsage &AU) const override{
>                   AU.addRequired<LoopInfoWrapperPass>();
>                   AU.setPreservesAll();
>               }
>               
>               bool runOnFunction(Function &F) override{
>                   errs() << "Hello: ";
>                   errs().write_escaped(F.getName()) << "\n";
>                   
>                   LoopInfo &LI = getAnalysis<LoopInfoWrapperPass>().getLoopInfo();
>                   int loopCounter = 0;
>                   for (LoopInfo::iterator i = LI.begin(), e = LI.end(); i != e; ++i) {
>                       loopCounter++;
>                   }
>                   
>                   errs() << "loop num:" << loopCounter << "\n";
>                   
>                   return false;
>               }
>           };
>       }
>       
>       char Hello::ID = 0;
>       
>       static RegisterPass<Hello> X("hello", "hello world pass", false, false);
>       
>       ```
>



#### 2、*LLVMSimplePass*（写的Pass只是把a+b简单的替换成了a-(-b),只是一个演示，怎么去写自己的Pass，并且作用于代码）



> * 1.创建头文件 :`llvm-3.9.0.src/include/llvm/Transforms  `统一存放头文件
>
>   ```
>   cd llvm/include/llvm/Transforms/
>   mkdir Obfuscation
>   cd Obfuscation
>   touch SimplePass.h
>   
>   ```
>
>   * 写入内容：
>
>     ```c++
>     #include "llvm/IR/Function.h"
>     #include "llvm/Pass.h"
>     #include "llvm/Support/raw_ostream.h"
>     #include "llvm/IR/Intrinsics.h"
>     #include "llvm/IR/Instructions.h"
>     #include "llvm/IR/LegacyPassManager.h"
>     #include "llvm/Transforms/IPO/PassManagerBuilder.h"
>     // Namespace
>     using namespace std;
>     namespace llvm {
>     	Pass *createSimplePass(bool flag);
>     }
>     
>     ```
>
> * 2.创建源文件： llvm-3.9.0.src/lib/Transforms  存放源代码
>
>
>
>   ```
>   cd llvm/lib/Transforms/
>   mkdir Obfuscation
>   cd Obfuscation
>   touch CMakeLists.txt
>   touch LLVMBuild.txt
>   touch SimplePass.cpp
>   
>   ```
>
>   * CMakeLists.txt
>
>     ```
>     add_llvm_loadable_module(LLVMObfuscation
>       SimplePass.cpp
>       
>       )
>       add_dependencies(LLVMObfuscation intrinsics_gen)
>     
>     ```
>
>   * LLVMBuild.txt:
>
>     ```
>     
>     [component_0]
>     type = Library
>     name = Obfuscation
>     parent = Transforms
>     library_name = Obfuscation
>     
>     ```
>
>   * SimplePass.cpp:
>
>     ```
>     #include "llvm/Transforms/Obfuscation/SimplePass.h"
>     using namespace llvm;
>     namespace {
>         struct SimplePass : public FunctionPass {
>             static char ID; // Pass identification, replacement for typeid
>             bool flag;
>              
>             SimplePass() : FunctionPass(ID) {}
>             SimplePass(bool flag) : FunctionPass(ID) {
>             	this->flag = flag;
>             }
>              
>             bool runOnFunction(Function &F) override {
>             	if(this->flag){
>                     Function *tmp = &F;
>                     // 遍历函数中的所有基本块
>                     for (Function::iterator bb = tmp->begin(); bb != tmp->end(); ++bb) {
>                         // 遍历基本块中的每条指令
>                         for (BasicBlock::iterator inst = bb->begin(); inst != bb->end(); ++inst) {
>                             // 是否是add指令
>                             if (inst->isBinaryOp()) {
>                                 if (inst->getOpcode() == Instruction::Add) {
>                                     ob_add(cast<BinaryOperator>(inst));
>                                 }
>                             }
>                         }
>                     }
>                 }
>                 return false;
>             }
>              
>             // a+b === a-(-b)
>             void ob_add(BinaryOperator *bo) {
>                 BinaryOperator *op = NULL;
>                  
>                 if (bo->getOpcode() == Instruction::Add) {
>                     // 生成 (－b)
>                     op = BinaryOperator::CreateNeg(bo->getOperand(1), "", bo);
>                     // 生成 a-(-b)
>                     op = BinaryOperator::Create(Instruction::Sub, bo->getOperand(0), op, "", bo);
>                      
>                     op->setHasNoSignedWrap(bo->hasNoSignedWrap());
>                     op->setHasNoUnsignedWrap(bo->hasNoUnsignedWrap());
>                 }
>                  
>                 // 替换所有出现该指令的地方
>                 bo->replaceAllUsesWith(op);
>             }
>         };
>     }
>      
>     char SimplePass::ID = 0;
>      
>     // 注册pass 命令行选项显示为simplepass
>     static RegisterPass<SimplePass> X("simplepass", "this is a Simple Pass");
>     Pass *llvm::createSimplePass() { return new SimplePass(); }
>     
>     ```
>
> * 修改`.../Transforms/LLVMBuild.txt`, 加上刚刚写的模块`Obfuscation`
>
>   ```
>   ;===- ./lib/Transforms/LLVMBuild.txt ---------------------------*- Conf -*--===;
>   ;
>   ;                     The LLVM Compiler Infrastructure
>   ;
>   ; This file is distributed under the University of Illinois Open Source
>   ; License. See LICENSE.TXT for details.
>   ;
>   ;===------------------------------------------------------------------------===;
>   ;
>   ; This is an LLVMBuild description file for the components in this subdirectory.
>   ;
>   ; For more information on the LLVMBuild system, please see:
>   ;
>   ;   http://llvm.org/docs/LLVMBuild.html
>   ;
>   ;===------------------------------------------------------------------------===;
>   
>   [common]
>   subdirectories = IPO InstCombine Instrumentation Scalar Utils Vectorize ObjCARC Obfuscation
>   
>   [component_0]
>   type = Group
>   name = Transforms
>   parent = Libraries
>   
>   ```
>
> * 修改`.../Transforms/CMakeLists.txt`, 加上刚刚写的模块`Obfuscation`
>
>   ```
>   add_subdirectory(Obfuscation)
>   
>   ```
>
> * 编译生成：`LLVMSimplePass.dylib`
>
>    
>
> * 因为Pass是作用于中间代码，所以我们首先要生成一份中间代码：
>
>   ```
>   clang -emit-llvm -c test.c -o test.bc
>   
>   ```
>
> * 然后加载Pass优化：
>
>   ```
>   ../build/bin/opt -load ../build/lib/LLVMSimplePass.dylib -simplepass < test.bc > after_test.bc
>   
>   ```
>
> * 
>



###### 将Pass加入PassManager管理



上面我们是单独去加载Pass动态库，这里我们将Pass加入PassManager，这样我们就可以直接通过clang的参数去加载我们的Pass了。

 

> * 首先在`llvm/lib/Transforms/IPO/PassManagerBuilder.cpp`添加头文件。
>
>   ```
>   //===- PassManagerBuilder.cpp - Build Standard Pass -----------------------===//
>   //
>   //                     The LLVM Compiler Infrastructure
>   //
>   // This file is distributed under the University of Illinois Open Source
>   // License. See LICENSE.TXT for details.
>   //
>   //===----------------------------------------------------------------------===//
>   //
>   // This file defines the PassManagerBuilder class, which is used to set up a
>   // "standard" optimization sequence suitable for languages like C and C++.
>   //
>   //===----------------------------------------------------------------------===//
>   
>   #include "llvm/Transforms/IPO/PassManagerBuilder.h"
>   #include "llvm-c/Transforms/PassManagerBuilder.h"
>   #include "llvm/ADT/SmallVector.h"
>   #include "llvm/Analysis/BasicAliasAnalysis.h"
>   #include "llvm/Analysis/CFLAndersAliasAnalysis.h"
>   #include "llvm/Analysis/CFLSteensAliasAnalysis.h"
>   #include "llvm/Analysis/GlobalsModRef.h"
>   #include "llvm/Analysis/Passes.h"
>   #include "llvm/Analysis/ScopedNoAliasAA.h"
>   #include "llvm/Analysis/TargetLibraryInfo.h"
>   #include "llvm/Analysis/TypeBasedAliasAnalysis.h"
>   #include "llvm/IR/DataLayout.h"
>   #include "llvm/IR/LegacyPassManager.h"
>   #include "llvm/IR/ModuleSummaryIndex.h"
>   #include "llvm/IR/Verifier.h"
>   #include "llvm/Support/CommandLine.h"
>   #include "llvm/Support/ManagedStatic.h"
>   #include "llvm/Target/TargetMachine.h"
>   #include "llvm/Transforms/IPO.h"
>   #include "llvm/Transforms/IPO/ForceFunctionAttrs.h"
>   #include "llvm/Transforms/IPO/FunctionAttrs.h"
>   #include "llvm/Transforms/IPO/InferFunctionAttrs.h"
>   #include "llvm/Transforms/Instrumentation.h"
>   #include "llvm/Transforms/Scalar.h"
>   #include "llvm/Transforms/Scalar/GVN.h"
>   #include "llvm/Transforms/Vectorize.h"
>   #include "llvm/Transforms/Obfuscation/SimplePass.h"
>   
>   using namespace llvm;
>   
>   ```
>
> * 添加如下code
>
>   ```
>   static cl::opt<bool> SimplePass("simplepass", cl::init(false),
>                              cl::desc("Enable simple pass"));
>   static cl::opt<bool>
>   RunLoopVectorization("vectorize-loops", cl::Hidden,
>                        cl::desc("Run the Loop vectorization passes"));
>   
>   ```
>
> * 然后在`populateModulePassManager`这个函数中添加如下代码：
>
>   ```
>   void PassManagerBuilder::populateModulePassManager(
>       legacy::PassManagerBase &MPM) {
>     // Allow forcing function attributes as a debugging and tuning aid.
>     MPM.add(createForceFunctionAttrsLegacyPass());
>   MPM.add(createSimplePass(SimplePass));
>   
>   ```
>
> * 最后在IPO这个目录的`LLVMBuild.txt`中添加库的支持，否则在编译的时候会提示链接错误。具体内容如下：
>
>   ```
>   ;===- ./lib/Transforms/IPO/LLVMBuild.txt -----------------------*- Conf -*--===;
>   ;
>   ;                     The LLVM Compiler Infrastructure
>   ;
>   ; This file is distributed under the University of Illinois Open Source
>   ; License. See LICENSE.TXT for details.
>   ;
>   ;===------------------------------------------------------------------------===;
>   ;
>   ; This is an LLVMBuild description file for the components in this subdirectory.
>   ;
>   ; For more information on the LLVMBuild system, please see:
>   ;
>   ;   http://llvm.org/docs/LLVMBuild.html
>   ;
>   ;===------------------------------------------------------------------------===;
>   
>   [component_0]
>   type = Library
>   name = IPO
>   parent = Transforms
>   library_name = ipo
>   required_libraries = Analysis Core InstCombine IRReader Linker Object ProfileData Scalar Support TransformUtils Vectorize Instrumentation Obfuscation
>   
>   ```
>
> * 修改Pass的CMakeLists.txt为静态库形式：
>
>   ```
>   add_llvm_library(LLVMipo
>     ArgumentPromotion.cpp
>     BarrierNoopPass.cpp
>     ConstantMerge.cpp
>     CrossDSOCFI.cpp
>     DeadArgumentElimination.cpp
>     ElimAvailExtern.cpp
>     ExtractGV.cpp
>     ForceFunctionAttrs.cpp
>     FunctionAttrs.cpp
>     FunctionImport.cpp
>     GlobalDCE.cpp
>     GlobalOpt.cpp
>     IPConstantPropagation.cpp
>     IPO.cpp
>     InferFunctionAttrs.cpp
>     InlineAlways.cpp
>     InlineSimple.cpp
>     Inliner.cpp
>     Internalize.cpp
>     LoopExtractor.cpp
>     LowerTypeTests.cpp
>     MergeFunctions.cpp
>     PartialInlining.cpp
>     PassManagerBuilder.cpp
>     PruneEH.cpp
>     SampleProfile.cpp
>     StripDeadPrototypes.cpp
>     StripSymbols.cpp
>     WholeProgramDevirt.cpp
>   
>     ADDITIONAL_HEADER_DIRS
>     ${LLVM_MAIN_INCLUDE_DIR}/llvm/Transforms
>     ${LLVM_MAIN_INCLUDE_DIR}/llvm/Transforms/IPO
>     )
>   
>   add_dependencies(LLVMipo intrinsics_gen)
>   
>   ```
>
> * 最后再编译一次。
>
>   * 那么我们可以这么去调用：
>
>     ```
>     ../build/bin/clang -mllvm -simplepass test.c -o after_test
>     
>     ```

基于Pass，我们可以做什么？ 我们可以编写自己的Pass去混淆代码，以增加他人反编译的难度。

![image](https://wx3.sinaimg.cn/large/af39b376gy1fuetapenhaj20mz0bdgnf.jpg)



#  IV、[OLLVM](https://github.com/AloneMonkey/iOSREBook/tree/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.4%20%E4%BB%A3%E7%A0%81%E6%B7%B7%E6%B7%86/TestOLLVM) 源码分析，并从基于clang4.0 迁移到clang6.0



> * [code](https://github.com/obfuscator-llvm/obfuscator/tree/llvm-4.0/lib/Transforms/Obfuscation): 基于llvm的代码混淆技术主要基于中间层代码的pass。
>
>   ![image](https://wx3.sinaimg.cn/large/af39b376gy1fueu397yqpj20vo0g2n0l.jpg)
>
>   * /lib/Transforms/CMakeLists.txt
>
>     ```
>     add_subdirectory(Utils)
>     add_subdirectory(Instrumentation)
>     add_subdirectory(InstCombine)
>     add_subdirectory(Scalar)
>     add_subdirectory(IPO)
>     add_subdirectory(Vectorize)
>     add_subdirectory(Hello)
>     add_subdirectory(ObjCARC)
>     add_subdirectory(Coroutines)
>     add_subdirectory(Obfuscation)
>     
>     ```
>
>   * lib/Transforms/Obfuscation/CMakeLists.txt
>
>     ```
>     add_llvm_library(LLVMObfuscation
>       CryptoUtils.cpp
>       Substitution.cpp
>       BogusControlFlow.cpp
>       Utils.cpp
>       SplitBasicBlocks.cpp
>       Flattening.cpp
>       )
>     
>     add_dependencies(LLVMObfuscation intrinsics_gen)
>     
>     ```
>
>     * 将`add_llvm_library` 修改为`add_llvm_loadable_module`编译成动态库，通过opt 加载调试（如果添加到passManager中，编译成静态库，同样可以通过opt加载调试）
>
>   * 主要包含的三个pass，用于不同程序的混淆
>
>     * BogusControlFlow： 增加虚假的控制流程和无用的代码
>     * Flattening： 使代码扁平化
>     * Substitution： 增肌算术表达式的复杂度



#### 编译生成动态库，分析BogusControlFlow

> * open xcode ,安装command+B ,选择LLVMObfuscation的scheme，编译生成动态库
>
> * 1） 分析BogusControlFlow
>
>   * [BogusControlFlow.cpp](https://github.com/zhangkn/obfuscator/blob/llvm-4.0/lib/Transforms/Obfuscation/BogusControlFlow.cpp)
>
>   * 编辑 scheme启动参数，添加-debug 来打印调试信息
>
>   * 在`runOnFunction` 下断点，该方法会在处理方法的时候自动调用
>
>     * 在runOnFunction 中先判断参数是否合法，在判断是否需要混淆，从而指定全混淆或者在满足某些函数属性的情况下混淆
>
>       * // Check if the percentage is correct
>
>         ```
>               if (ObfTimes <= 0) {
>                 errs()<<"BogusControlFlow application number -bcf_loop=x must be x > 0";
>         		return false;
>               }
>         
>         ```
>
>       * // Check if the number of applications is correct
>
>         ```
>               if ( !((ObfProbRate > 0) && (ObfProbRate <= 100)) ) {
>                 errs()<<"BogusControlFlow application basic blocks percentage -bcf_prob=x must be 0 < x <= 100";
>         		return false;
>               }
>         
>         ```
>
>       * // If fla annotations
>
>         ```
>               if(toObfuscate(flag,&F,"bcf")) {
>                 bogus(F);
>                 doF(*F.getParent());
>                 return true;
>               }
>         
>         ```
>
>         * void bogus(Function &F) { 分析
>
>           * // Put all the function's block in a list  把方法中的所有block放到一个list  中。然后分别取出，调用`addBogusFlow`增加虚假控制流程
>
>             ```
>                           addBogusFlow(basicBlock, F);//     * Add bogus flow to a given basic block, according to the header's description
>             
>                 virtual void addBogusFlow(BasicBlock * basicBlock, Function &F){
>             
>             ```
>
>           * `addBogusFlow` 首先调用`createAlteredBasicBlock`复制原来的BasicBlock，然后修改其变量，引用并插入一些脏数据，接着创建一个恒为true的条件跳转到原来BasicBlock。false的分支跳转到复制的BasicBlock，复制的BasicBlock跳转回原来的BasicBlock。最后，创建一个恒为true的条件跳转到原BasicBlock的结束位置，false分支跳转到复制的BasicBlock。
>
>             * `BasicBlock *alteredBB = createAlteredBasicBlock(originalBB, *var3, &F);`
>
>   * 使用[**Graphviz**](http://graphviz.org/) 查看流程图，设置**dot**文件的默认打开方式。
>   * 调试过程使用`po originalBB->dump()` 打印对应的结果信息及浏览当前的流程图。
>   * po F.viewCFG()
>   * build/Debug/llvm-dis test_after.bc -o test_after.ll` 将混淆处理之后的bitcode转换为可读的bitcode代码
>
> * 2）[Flattening.cpp](https://github.com/zhangkn/obfuscator/blob/llvm-4.0/lib/Transforms/Obfuscation/Flattening.cpp) 扁平化功能的实现分析
>
>   * `opt -load ./lib/LLVMObfusaction.dylib -debug -flattening path/to/test.bc -o path/to/test_atfer.bc`
>
>     * 一旦进入runOnFunction 函数，判断是否需要混淆；如果需要混淆，就调用flatten 方法，保存所有的BasicBlock；如果个数小于等于1 就认为`Nothing to flatten` 直接返回；然后移除第一个BasicBlock`Remove first BB`.
>
>     * ` Get a pointer on the first BB` 接着判断第一个BasicBlock是不是条件判断或者分支，如果是就使用splitBasicBlock 将其分开，并移除第一个BasicBlock。
>
>     * 分别创建一个loopEntry和一个loopEnd的BasicBlock，分别将第一个BasicBlock和loopEnd跳转到loopEntry，创建一个指向loopEnd的switchDefault，并将loopEntry 指向switchDefault。
>
>     * `Put all BB in the switch` 将之前保存的BasicBlock分别添加到switch分支中。
>
>     * 将switch中的所有的BasicBlock 指向loopEnd  并调整switch case。
>
> * 3）最后一个Substitution 将一些计算指令替换成一些复杂的转换表达式（这种混淆在开启编译器优化之后都会失效，除非指定了不优化函数）



 # 

# See Also 

>* https://github.com/zhangkn/obfuscator
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

