---
layout: post
title: DerekSelander_LLDB
date: 2018-07-07
tag: Debug
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions
---

# 前言



lldb 会默认从`~/.lldbinit `加载自定义脚本。新增command script之后，重启Xcode，或者直接在lldb交互模式下输入`command source ~/.lldbinit`来加载脚本。

# Installation

> * git clone 
>
>   ```
>   ➜  lldb git clone git@github.com:DerekSelander/LLDB.git
>   ```
>
>   ```
>   1、To Install, copy the lldb_commands folder to a dir of your choosing.
>   2、Open up (or create) ~/.lldbinit
>   3、Add the following command to your ~/.lldbinit file: command script import /path/to/lldb_commands/dslldb.py
>   ```
>
>   > * import script 
>   >
>   >   ```
>   >   echo -e "\ncommand script import /Users/devzkn/code/lldb/LLDB/lldb_commands/dslldb.py" >> ~/.lldbinit
>   >   ```
>   >
>   >   

> * 同时推荐安装`brew install chisel`
>
>   ```
>   touch .lldbinit 
>   open .lldbinit
>   command script import /usr/local/opt/chisel/libexec/fblldb.py
>   ```
>
>   

# 例子

#### 定位按钮的action事件: 对应的oc API `[0x10b35a6d0 actionsForTarget:0x10a7caeb0 forControlEvent:64]`

> * 使用python脚本之后，就不用通过po 按钮的方法3次进行定位，而只需pactions 一次就行了
>
>    ![img](https://wx4.sinaimg.cn/large/af39b376gy1ft19i7w670j20sq06labb.jpg)



#### 查看控件的响应链: 对应私有API：`nextResponder`

> * **presponder** 
>
>   ![image](https://wx4.sinaimg.cn/large/af39b376gy1ft1a2wphj2j20x604cmyy.jpg)

#### 查看界面控件和布局

> * 使用Xcode自带的《debug View Hierarchy》
>
> * **pviews** : 由`fblldb.py`提供
>
> * 使用私有API
>
>   ```
>   [[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
>   ```
>
>   ```
>   [UIWindow keyWindow].recursiveDescription().toString()
>   ```
>
>   > * 基于layout展示View架构
>
>   ```
>   cy# [[UIWindow keyWindow] _autolayoutTrace].toString()
>   ```

####  查看`block`参数

###### 没有参数的block

> * `typedef void (^dispatch_block_t)(void); `

![image](https://wx4.sinaimg.cn/large/af39b376gy1ft1bcci8qrj20ta01rweu.jpg)

> * 获取block的地址
>
>   * **register read --all** : 查看General Purpose Registers、Floating Point Registers、Exception State Registers 
>
>   ```
>   (lldb) po (char *)$x23
>   <__NSMallocBlock__: 0x1140c7060>
>   
>   (lldb) po $x23
>   <__NSMallocBlock__: 0x1140c7060>
>   (lldb) pblock 0x1140c7060  # 应当block的参数类型比较特殊，比如ID什么的，不是具体的类型
>   Traceback (most recent call last):
>     File "/usr/local/opt/chisel/libexec/fblldb.py", line 84, in runCommand
>       command.run(args, options)
>     File "/usr/local/Cellar/chisel/1.8.0/libexec/commands/FBClassDump.py", line 142, in run
>       signature = json['signature']
>   TypeError: 'NoneType' object has no attribute '__getitem__'
>   ```
>
>   > * dispatch_block_t
>
>   ```
>           dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
>               //xxx
>           });
>   
>   ```
>
>   * ```
>     (lldb) po $x19
>     <__NSMallocBlock__: 0x1172ea120>
>     (lldb) pblock 0x1172ea120
>     Imp: 0x10690b24c    Signature: void ^();
>     (lldb) po $x23
>     <__NSMallocBlock__: 0x1172ea120>
>     ```
>
>   * 

######  有参数的block参数

> * `typedef void(^FLSocketDidCloseBlock)(NSInteger code,NSString *reason,BOOL wasClean); `

![image](https://wx4.sinaimg.cn/large/af39b376gy1ft1bt0np35j20lt029glz.jpg)

> * 下断点，可以使用methods 或者`_shortMethodDescription`获取方法地址
>
> * 获取寄存器的值： register read --all
>
>   ```
>          x23 = 0x000000011bb4e180
>          x24 = 0x00000001069f1240  libdispatch.dylib`_dispatch_call_block_and_release
>          x25 = 0x0000000106add938  SocketRocket`__27-[SRWebSocket _pumpWriting]_block_invoke at SRWebSocket.m:1193
>   ```
>
>   ```
>   (lldb) po $x8
>   <__NSMallocBlock__: 0x11bb4e380>
>   
>   (lldb) pblock 0x11bb4e380
>   Imp: 0x10698bc18    Signature: void ^(long long, NSString *, bool);
>   ```
>
>   



#### 查看特定class的所有方法以及对应的内存地址，直接下断点

> * **methods** 
>
>   * 获取方法地址
>
>     ```
>     (lldb) methods 0x10b45cb90
>     <KNSocketManagerTool: 0x10b45cb90>:
>     in KNSocketManagerTool:
>     	Class Methods:
>     		+ (id) shareSocketManagerTool; (0x1069a1510)
>     		+ (id) allocWithZone:(struct _NSZone*)arg1; (0x1069a167c)
>     	Properties:
>     		@property (retain, nonatomic) GACConnectConfig* connectConfig;  (@synthesize connectConfig = _connectConfig;)
>     	Instance Methods:
>     		- (void) SRWebSocketClose; (0x1069a3b50)
>     ```
>
>     * 对SRWebSocketClose 进行下断点
>
>       ```
>       b 0x1069a3b50
>       Breakpoint 2: where = libweixinDylib.dylib`-[KNSocketManagerTool SRWebSocketClose] at KNSocketManagerTool.m:252, address = 0x00000001069a3b50
>       ```
>
>       * breakpoint delete 
>
> * 通过_shortMethodDescription 用于LLDB 进行方法打断点的步骤：
>
>   * LLDB连接到程序
>   * 找到需要下断点的类，如CMessageMgr，然后在LLDB命令行输入po [className
>     _shortMethodDescription]，来查看方法对应的内存地址
>   * `b 内存地址` 下断点；这样的好处是避免ASRL偏移量的计算
>
>    
>
>   



#### 打印class 对应的对象数组： search 控件名称 《=》 cycript 的choose

> * search UITextFild
>
>   ```
>   (lldb) search UITextField
>   <UISearchBarTextField: 0x114114bd0; frame = (8 8; 304 28); text = ''; opaque = NO; gestureRecognizers = <NSArray: 0x116d27e40>; layer = <CALayer: 0x114115b40>>
>   ```
>
>   
>
> * 定位UIButton class 对应的对象，返回的是数组
>
>   ```
>   cy# choose(UIButton).toString() 
>   ```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost DerekSelander_LLDB A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions -t Debug
> #原来""的参数，需要自己加上""
```

