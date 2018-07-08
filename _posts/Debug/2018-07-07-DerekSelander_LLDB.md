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

#### pblock 的源码 

> * https://github.com/kunnan/chisel/blob/master/commands/FBClassDump.py
>
>   ```
>   /usr/local/Cellar/chisel/1.8.0/libexec/commands/FBClassDump.py
>   class FBPrintBlock(fb.FBCommand):
>     def run(self, arguments, options):
>           struct Block_descriptor_1 {
>                     const char *signature;                         // IFF (1<<30)
>   ```
>
>   ```
>   def getMethods(klass):
>       Method *methods = (Method *)class_copyMethodList((Class)$cls, &outCount);
>         SEL name = (SEL)method_getName(methods[i]);
>               char * encoding = (char *)method_getTypeEncoding(methods[i]);
>         NSInteger args = (NSInteger)method_getNumberOfArguments(methods[i]);
>   
>   ```
>
>   

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
>   (lldb) pblock 0x1140c7060  # 应当block的参数类型比较特殊，比如ID什么的，不是具体的类型；或者当前的断点并不在block内部，才导致失败的
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



# other

####   查看地址所在模块的信息: `image lookup -a 0x1922da0c8 `

> * `-[UIApplication(UIApplicationKeyboardTesting) runTestForKeyboardBringupAndDismissalWithName:withShowKeyboardBlock:withHideKeyboardBlock:withExtraResultsBlock:withCleanupBlock:] `在文件中的偏移为：0x0000000187c720c8 
>
>   ```
>   (lldb) image lookup -a 0x1922da0c8
>         Address: UIKit[0x0000000187c720c8] (UIKit.__TEXT.__text + 7688388)
>         Summary: UIKit`-[UIApplication(UIApplicationKeyboardTesting) runTestForKeyboardBringupAndDismissalWithName:withShowKeyboardBlock:withHideKeyboardBlock:withExtraResultsBlock:withCleanupBlock:]
>   ```
>
>   
>
>   



#### 在模块中查看与`xxx` 相关的符号信息

> * **image lookup -rn** xxx
>
>   ```
>   (lldb) image lookup -rn CContassctMgr 
>   18 matches found in /Users/devzkn/Library/Developer/Xcode/DerivedData/weixin-fqltqjyxecppixbrhoqzzbtyrgmz/Build/Products/Debug-iphoneos/weixin.app/Frameworks/libweixinDylib.dylib:
>           Address: libweixinDylib.dylib[0x0000000000038668] (libweixinDylib.dylib.__TEXT.__text + 200068)
>           Summary: libweixinDylib.dylib`_logos_meta_method$friend$CContactMgr$setupdoVerifybywxid$greetings$(objc_class*, objc_selector*, NSString*, NSString*) at weixinDylib.xm:490        Address: libweixinDylib.dylib[0x0000000000037a9c] (libweixinDylib.dylib.__TEXT.__text + 197048)
>   ```
>
>   

# Custom Commands：



#### 使用正则`command regex`，进行 自定义命令



###### 对类的所有方法下断点并跟踪打印调用参数

> * bclass: `help rb     Sets a breakpoint or set of breakpoints in the executable.`; 对类的所有方法进行下断点
>
> * ```
>   command regex bclass 's/(.+)/rb \[%1 /'
>   ```
>
>   ```
>   command source ~/.lldbinit
>   ```
>
>   ```
>   Executing commands in '/Users/devzkn/.lldbinit'.
>   command regex bclass 's/(.+)/rb \[%1 /'
>   command regex ls 's/(.+)/po @import Foundation; [[NSFileManager
>   defaultManager] contentsOfDirectoryAtPath:@"%1" error:nil]/'
>   command regex dump_stuff "s/(.+)/image lookup -rn '\+\[\w+(\(\w+\))?\ \w+
>   \]$' %1 /"
>   command script import /usr/local/opt/chisel/libexec/fblldb.py
>   command script import /Users/devzkn/code/lldb/LLDB/lldb_commands/dslldb.py
>   ```
>
>   ```
>   (lldb) bclass FLSocketManager
>   Breakpoint 5: 41 locations.
>   ```
>
>   
>
> * 跟踪打印调用参数: 给断点添加命令
>
>   ```
>   br command add 5
>   (lldb) br command add 5
>   Enter your debugger command(s).  Type 'DONE' to end.
>   > po $x0
>   > x/s $x1
>   > c
>   > DONE
>   ```
>
>   * 运行效果
>
>     ```
>      po $x0
>     FLSocketManager
>      x/s $x1
>     0x1069f1ef3: "shareManager"
>      c
>     Process 574 resuming
>     Command #3 'c' continued the target.
>      po $x0
>     <FLSocketManager: 0x10bc62e70>
>      x/s $x1
>     0x1069f1f40: "fl_socketStatus"
>     Process 574 resuming
>     Command #3 'c' continued the target.
>      po $x0
>     FLSocketManager
>     
>     
>      x/s $x1
>     0x1069f1ef3: "shareManager"
>     
>      c
>     Process 574 resuming
>     
>     Command #3 'c' continued the target.
>      po $x0
>     <FLSocketManager: 0x10bc62e70>
>     
>     
>      x/s $x1
>     0x1069f22ff: "fl_sendping:"
>     
>      c
>     Process 574 resuming
>     
>     Command #3 'c' continued the target.
>      po $x0
>     FLSocketManager
>     
>     
>      x/s $x1
>     0x1069f1ef3: "shareManager"
>     
>      c
>     Process 574 resuming
>     
>     Command #3 'c' continued the target.
>      po $x0
>     <FLSocketManager: 0x10bc62e70>
>     
>     
>      x/s $x1
>     0x1069f1f40: "fl_socketStatus"
>     
>      c
>     Process 574 resuming
>     
>     Command #3 'c' continued the target.
>     2018-07-07 17:34:35.776424 WeChat[574:252555] 发送中。。。
>      po $x0
>     <FLSocketManager: 0x10bc62e70>
>     
>     
>      x/s $x1
>     0x1069f1f50: "webSocket"
>     
>      c
>     Process 574 resuming
>     
>     Command #3 'c' continued the target.
>      po $x0
>     <FLSocketManager: 0x10bc62e70>
>     
>     
>      x/s $x1
>     0x106afee54: "webSocket:didReceivePong:"
>     ```
>
>     



###### ls 、dump_stuff

> * ls : `po `
>
>   ```
>   command regex ls 's/(.+)/po @import Foundation; [[NSFileManager
>   defaultManager] contentsOfDirectoryAtPath:@"%1" error:nil]/'
>   
>   ```
>
>   ```
>   (lldb) ls /
>   <__NSArrayM 0x1195f2fa0>(
>   .Trashes,
>   .cydia_no_stash,
>   .file,
>   .fseventsd,
>   .installed_yaluX,
>   Applications,
>   Developer,
>   Library,
>   System,
>   User,
>   bin,
>   boot,
>   cores,
>   dev,
>   etc,
>   lib,
>   mnt,
>   private,
>   sbin,
>   tmp,
>   usr,
>   var
>   )
>   
>   ```
>
>   
>
> * dump_stuff: `image looup`
>
>   
>
>   ```
>   command regex dump_stuff "s/(.+)/image lookup -rn '\+\[\w+(\(\w+\))?\ \w+
>   \]$' %1 /"
>   ```
>
>   

#### `#!/usr/bin/python` 脚本调用lldb API 进行自定义命令

> *  source the commands in lldbinit
>
>   ```
>   command script import /path/to/fblldb.py
>   script fblldb.loadCommandsInDirectory('/magical/commands/') ##loadCommandsInDirectory in the fblldb.py module
>   ```
>
>   
>
> * example
>
>   ```
>   #!/usr/bin/python
>   # Example file with custom commands, located at /magical/commands/example.py
>   
>   import lldb
>   import fblldbbase as fb
>   
>   def lldbcommands():
>     return [ PrintKeyWindowLevel() ]
>     
>   class PrintKeyWindowLevel(fb.FBCommand):
>     def name(self):
>       return 'pkeywinlevel'
>       
>     def description(self):
>       return 'An incredibly contrived command that prints the window level of the key window.'
>       
>     def run(self, arguments, options):
>       # It's a good habit to explicitly cast the type of all return
>       # values and arguments. LLDB can't always find them on its own.
>       lldb.debugger.HandleCommand('p (CGFloat)[(id)[(id)[UIApplication sharedApplication] keyWindow] windowLevel]')
>   
>   ```
>
>   

#### 小结

> * 这两种方式最终都是可以从`~/.lldbinit`加载；

> * 使用python脚本利用lldb的API进行自定义往往提供更强大的功能，更利于维护。
> * 使用`command regex`往往是对现有原生的`command`进行进一步简单封装。

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost DerekSelander_LLDB A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions -t Debug
> #原来""的参数，需要自己加上""
```

