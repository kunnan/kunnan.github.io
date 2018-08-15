---
layout: post
title: Dyld_Introduction
date: 2018-08-14
tag: dyld
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 动态库： 静态库、动态库的区别，编译和注入；导出和隐藏符号表；
---





# 静态库和动态库的区别

![image](https://wx3.sinaimg.cn/large/af39b376gy1fu997tvkbhj20ca0egmyr.jpg)

* 动态库的特点

  * 存在形式有 .dylib，.framework 和链接符号 .tdb;
  * 它的好处是可以只保留一份文件和内存空间，从而能够被多个进程使用，例如系统动态库；
  * 可减小可执行文件的体积，不需要链接到目标文件。

   

   

   

* 静态库的特点

  * 以.a 或者.framework形式存在的一种共享程序代码的方式，从本质上来讲就是一种可执行文件的二进制形式；常常会将程序的部分功能编译成库，暴露出头文件的形式供开发者调用
  * 静态库以一个或者多个object文件组成；可以将一个静态库拆解成多个object文件（ar -x）
  * 静态库链接的时会直接链接到目标文件，并作为它的一部分存在。

# I 、[动态库的编译和注入](https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93)



> * 编译
>
>   * `xcrun --sdk iphoneos clang++ dynamiclib -arch arm64  -framework Foundation Person.mm -o  target.dylib  -fvisibility=hidden` 
>   * [Makefile: `$(CC) -dynamiclib -arch $(ARCH) $(FRAMEWORK) $(SOURCE) -o $(TARGET) $(VERSION)`](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/OC%20dylib/Makefile)
>
> * 动态库注入放入几种方式
>
>   * 通过增加load command 的`LC_LOAD_DYLIB`或者`LC_LOAD_WEAK_DYLIB`,指定动态库的路径来实现注入
>
>     * optool
>     * insert_dylib
>     *  如果需要修改`LC_ID_DYLIDB、`、`LC_LOAD_DYLIB`,可以使用`install_name_tool`
>       * `install_name_toll -id xxx imputfile`
>       * `install_name_toll -change old new imputfile`
>
>   * 通过`cydia substrate`提高的注入，配置plist文件，并将对应的plist、dylib文件放入指定目录（ /Layout/Library/MobileSubstrate/DynamicLibraries/、/usr/lib/TweakInject）；其实也是通过`DYLD_INSERT_LIBRARIES`将自己注入，然后遍历`DynamicLibraries`目录下的plist文件，再将符合规则的动态库通过`dlopen`打开
>
>   * 通过设置环境变量`DYLD_INSERT_LIBRARIES`指定要注入的动态库path
>
>     * 利用环境变量 DYLD_INSERT_LIBRARY 来添加动态库dumpdecrypted.dylib
>
>       ```
>       DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/01ECB9D1-858D-4BC6-90CE-922942460859/KNWeChat.app/KNWeChat
>       
>       ```
>
>       

#    导出和隐藏符号



> * ` nm  -gm tmp_64.dylib ` 查看导出符号信息
>
>   ```
>   (__DATA,__data) external
>   (undefined) external _CFDataCreate (from CoreFoundation)
>                    (undefined) external _CFNotificationCenterGetDarwinNotifyCenter (from CoreFoundation)
>    (__TEXT,__text) external 
>                     (undefined) external _IOObjectRelease (from IOKit)
>                    (undefined) external _IORegistryEntryCreateCFProperty (from IOKit)
>   000000010ffa3f97 (__DATA,__objc_data) external _OBJC_CLASS_$_BslyjNwZmPCJkVst
>   000000010ffa3f97 (__DATA,__objc_data) external _OBJC_CLASS_$_ChiDDQmRSQpwQJgm
>   
>   ```
>
>   
>
> * 控制符号是否导出
>
>   * static 参数修饰,不会导出符号信息
>
>     ```
>     static char _person_name[30] = {'\0'};
>     
>     ```
>
>     
>
>   * [在编译参数中加入-exported_symbols_list export_list](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/ExportSymbol/Makefile)
>
>   * [在编译参数中指定-fvisibility=hidden，对指定符号增加visibility("default")来导出符号](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/OC%20dylib/Makefile)
>
>     ```
>     //#define EXPORT __attribute__((visibility("default")))
>     
>     ```
>
>     



# [使用`dlopen` 动态的方式调用c++、oc编写的动态库](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/TargetApp/TargetApp/main.mm)



#### c++ 

> * dlopen
>
>   * [c++ dylib code](https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/C%2B%2B%20dylib)
>   *    `这里的符号是通过c    导出的，所以直接写名字；如果是c++ 导出的，就要指定c++  的符号,    这点和获取swift类时是一样的。`
>
>   ```
>   //        void *lib_handle = dlopen("/Library/MobileSubstrate/DynamicLibraries/target.dylib", RTLD_LOCAL);
>   //        if(!lib_handle){
>   //            NSLog(@"Unable to open library: %s",dlerror());
>   //            return 0;
>   //        }
>   //
>   //        Person_creator* NewPerson = (Person_creator*)dlsym(lib_handle, "_Z9NewPersonv");
>   //        if(!NewPerson){
>   //            NSLog(@"Unable to find NewPerson method:%s",dlerror());
>   //            return 0;
>   //        }
>   //
>   //        Person_disposer* DeletePerson = (Person_disposer*)dlsym(lib_handle, "_Z12DeletePersonP6Person");
>   //        if(!DeletePerson){
>   //            NSLog(@"Unable to find DeletePerson method:%s",dlerror());
>   //            return 0;
>   //        }
>   //
>   //        Person* person = (Person*)NewPerson();
>   //
>   //        NSLog(@"person->name() = %s",person->name());
>   //
>   //        char new_name[] = "AloneMonkey";
>   //
>   //        person->set_name(new_name);
>   //
>   //        NSLog(@"person->name() = %s",person->name());
>   //
>   //        DeletePerson(person);
>   //
>   //        if(dlclose(lib_handle) != 0){
>   //            NSLog(@"Unable to close library: %s",dlerror());
>   //        }
>   
>   
>   ```
>
>   

#### oc

> * dlopen 
>
>   * oc 的动态库和正常使用动态库没有区别
>   * [oc dylib code](https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/OC%20dylib)
>
>   
>
>   
>
>   ```
>           // Open the library.
>           void* lib_handle = dlopen("/Library/MobileSubstrate/DynamicLibraries/target.dylib", RTLD_LOCAL);
>           if (!lib_handle) {
>               NSLog(@"[%s] main: Unable to open library: %s\n",
>                     __FILE__, dlerror());
>               exit(EXIT_FAILURE);
>           }
>           
>           Class PersonClass = objc_getClass("Person");
>           if (!PersonClass) {
>               NSLog(@"[%s] main: Unable to get Person class", __FILE__);
>               exit(EXIT_FAILURE);
>           }
>           
>           NSLog(@"[%s] main: Instantiating PersonClass", __FILE__);
>           Person* person = [PersonClass new];
>           
>           [person setName:@"Alone Monkey"];
>           NSLog(@"[%s] main: [person name] = %@", __FILE__, [person name]);
>           
>           if (dlclose(lib_handle) != 0) {
>               NSLog(@"[%s] Unable to close library: %s\n",
>                     __FILE__, dlerror());
>               exit(EXIT_FAILURE);
>           }
>   
>   ```
>
>   

# swift 




 > * getenv
>
>   ```
>   char * getenv(const char *name) {
>     static void *handle;      // 1
>     static char * (*real_getenv)(const char *); // 2
>     static dispatch_once_t onceToken;
>     dispatch_once(&onceToken, ^{  // 3
>       handle = dlopen("/usr/lib/system/libsystem_c.dylib", RTLD_NOW);
>       assert(handle);
>       real_getenv = dlsym(handle, "getenv");
>   });
>     if (strcmp(name, "HOME") == 0) { // 4
>   return "/"; }
>     return real_getenv(name); // 5
>   }
>   
>   ```
>
>   
>
> * dlopen
>
>   ```
>   if let handle = dlopen("./Frameworks/HookingSwift.framework/
>   HookingSwift", RTLD_NOW) {
>   }
>         let sym = dlsym(handle, "_TFC12HookingSwift23CopyrightImageGeneratorgP33_71AD57F3ABD678B113CF3AD05D01FF4113originalImageGSqCSo7UIImage_")!
>               print("\(sym)")
>   
>   ```
>
>   



>* [HookingSwift](https://segmentfault.com/a/1190000011678605)
>
>  ```
>  import UIKit
>  import HookingSwift
>  
>  class ViewController: UIViewController {
>  
>    // MARK: - IBOutlets
>    @IBOutlet weak var imageView: UIImageView!
>  
>    // MARK: - View Life Cycle
>    override func viewDidLoad() {
>      super.viewDidLoad()
>      
>      let imageGenerator = CopyrightImageGenerator()
>      imageView.image = imageGenerator.watermarkedImage
>      
>      if let handle = dlopen("./Frameworks/HookingSwift.framework/HookingSwift", RTLD_NOW) {
>        let sym = dlsym(handle, "_TFC12HookingSwift23CopyrightImageGeneratorgP33_71AD57F3ABD678B113CF3AD05D01FF4113originalImageGSqCSo7UIImage_")!
>        typealias privateMethodAlias = @convention(c) (Any) -> UIImage?
>        let originalImageFunction = unsafeBitCast(sym, to: privateMethodAlias.self)
>        let originalImage = originalImageFunction(imageGenerator)
>        imageView.image = originalImage
>      }
>    }
>  }
>  
>  ```
>





#### 使用`capstone Hook dylib`

```
#import <substrate.h> #import "capstone.h" static CFStringRef (*old_MGCA)(CFStringRef Key);
CFStringRef new_MGCA(CFStringRef Key){
        CFStringRef Ret=old_MGCA(Key);
        NSLog(@"MGHooker:%@\nReturn Value:%@",Key,Ret);
        return Ret;
}
%ctor {
        void * Symbol=MSFindSymbol(MSGetImageByName("/usr/lib/libMobileGestalt.dylib"), "_MGCopyAnswer");
        NSLog(@"MG: %p",Symbol);
        csh handle;
        cs_insn *insn;
        cs_insn BLInstruction;
        size_t count;
        unsigned long realMGAddress=0;
        //MSHookFunction(Symbol,(void*)new_MGCA, (void**)&old_MGCA);
        if (cs_open(CS_ARCH_ARM64, CS_MODE_ARM, &handle) == CS_ERR_OK) {
          /*cs_disasm(csh handle, const uint8_t *code, size_t code_size, uint64_t address, size_t count, cs_insn **insn);*/
                count=cs_disasm(handle,(const uint8_t *)Symbol,0x1000,(uint64_t)Symbol,0,&insn);
                if (count > 0) {
                        NSLog(@"Found %lu instructions",count);
                        for (size_t j = 0; j < count; j++) {
                              NSLog(@"0x%" PRIx64 ":\t%s\t\t%s\n", insn[j].address, insn[j].mnemonic,insn[j].op_str);
                                if(insn[j].id==ARM64_INS_B){
                                  BLInstruction=insn[j];
                                  sscanf(BLInstruction.op_str, "#%lx", &realMGAddress);
                                  break;
                                }
                        }

                        cs_free(insn, count);
                } else{
                  NSLog(@"ERROR: Failed to disassemble given code!%i \n",cs_errno(handle));
                }


                cs_close(&handle);

                //Now perform actual hook
                MSHookFunction((void*)realMGAddress,(void*)new_MGCA, (void**)&old_MGCA);
}
else{
        NSLog(@"MGHooker: CSE Failed");
}
}


```

> * [capstoneHook32](https://kunnan.github.io/2018/07/16/capstoneHook64_capstoneHook32/)

# See Also 


>  
>
>* [dumpdecrypted](https://zhangkn.github.io/2017/12/dumpdecrypted/)
>
>* [iOSMixProject:马甲包混淆工程](https://github.com/kunnan/iOSMixProject)
>

>  * [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dyld_Introduction 动态库： 静态库、动态库的区别，编译和注入；导出和银川符号表； -t dyld
> #原来""的参数，需要自己加上""
```

