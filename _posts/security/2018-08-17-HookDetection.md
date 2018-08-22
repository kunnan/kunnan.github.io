---
layout: post
title: HookDetection
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 反注入：注入检查机制，获取加载的模块名，判断是否都在白名单中;hook 检测：method swizzle、符号表替换、inline hook
---



# 前言

[针对注入的方式进行反注入，现在先回一下注入的方式有哪些？](https://kunnan.github.io/2018/08/14/hook_methods_in_ios/)

> - iOS 中常见的hook方式
>
>   - [hooking-swift-methods](https://kunnan.github.io/2018/06/06/hooking-swift-methods/)
>
>   - [Hooking & Executing Code with dlopen & dlsym —Easy mode:hooking C methods](https://github.com/zhangkn/hookingCmethods)
>
>   - 利用[运行时AP](https://kunnan.github.io/tags/#Runtime)I ，Method Swizzling ： 通过修改oc 函数的IMP达到替换方法实现的目的
>
>   - [fishhook：A library that enables dynamically rebinding symbols in Mach-O binaries running on iOS.](https://github.com/facebook/fishhook) 通过修改内存中懒加载和非懒加载符号表指针所指向的地址来达到修改方法的目的，作用于主模块懒加载和非懒加载表的符号，在越狱和非越狱环境都可以使用。
>
>     - facebook 开源的一个非常小的重新绑定动态符号的库
>
>     - How it works
>
>       ![image](https://wx3.sinaimg.cn/large/af39b376gy1fu976jnsf3j20jo0pc76u.jpg)
>
>   - [substitute:A free runtime modification library.](https://github.com/coolstar/substitute)
>
>   - cydia substrate: `通过inline hook的方式修改目标函数内存中的汇编指令，使其调转到自己的代码块，以达到修改程序的目的，同时支持method swizzle`
>
>     - `__attribute__((constructor))`
>
>       ```
>       static __attribute__((constructor)) void myinit() 
>       ```
>
>     - ios11 使用Electra 越狱之后，存放dylib的path: `/usr/lib/TweakInject`
>
>       - [docs/getting-started.md](https://github.com/coolstar/electra/blob/master/docs/getting-started.md)
>
>         - Substitute is used as the hooking framework instead of substrate
>
>       - [Electra](https://kunnan.github.io/2018/08/14/hook_methods_in_ios/Electra%20iOS%2011.0%20-%2011.1.2%20jailbreak%20toolkit%20based%20on%20async_awake)
>
>       - [electra1131](https://github.com/coolstar/electra1131)
>
>       - [electra-ipas](https://github.com/coolstar/electra-ipas)
>
>       - Framework 存放的path
>
>         ```
>             cp -r ~/Projects/_window/LatestBuild/AFNetworking.framework ~/Layout/System/Library/Frameworks/
>                 
>         ```
>
>       - 干脆给他建立个软连接算了 ` /bin/ln -s /usr/lib/TweakInject /Library/MobileSubstrate/DynamicLibraries `
>
>         - 先备份` cp -r /Library/MobileSubstrate/DynamicLibraries ~/`，再删除，再创建ln
>
>           ```
>                # rm -rf /Library/MobileSubstrate/DynamicLibraries
>           ```
>
>     - `/Library/MobileSubstrate/DynamicLibraries/`

 



# restrict section 

 当旧版的dylb 检测到存在`__RESTRICT`、`__restrict` 这样的section时候，DYLD_INSERT_LIBRARIES 环境变量会被忽略，导致注入失败。

> * 在Project的 `Other Linker Flags` 增加 
>
>   ```
>   -Wl,-sectcreate,__RESTRICT,__restrict,/dev/null
>   
>   ```
>



在新版的dylb以及iOS10中，发现dylb 已经不检测这个section.而且option 自带unrestrict。



> * 去掉保护: anti RESTRICT
>
>   * `ps -e | grep /var` 找到AppBinary路径
>
>   * 二进制编辑器（iHex等）修改__RESTRICT和__restrict为其他值。（比如：__RRRRRRRR和__rrrrrrrr。保证长度不变就行啦）
>
>   * `ldid -S AppBinary` 重签名。或者Cydia中安装 `AppSync`。
>
>
>    
>
>    
>
>    
>



# 注入检测

通过`_dylb_get_image_name`判断加载模块中是否有异常进行注入检测

[search _dyld_get_image_name](https://github.com/AloneMonkey/iOSREBook/search?p=2&q=_dyld_get_image_name&type=&utf8=%E2%9C%93)



> * `const char* *_dyld_get_image_name*(uint32_t image_index)`
>
>   * 发现其他不在白名单内的库:`调用 _dyld_image_count() 获得加载的动态库的数量，_dyld_get_image_name() 获得名字，然后遍历他们的名字，看看有没有 “MobileSubstrate” 关键字，有的话就是越狱的`
>
>     ```
>     int ckeckInjector() 
>     {
>     	// see if libSystem is in list of images
>     	uint32_t count = _dyld_image_count();
>     	for(uint32_t i=0; i < count; ++i) {
>     		const char*  name = _dyld_get_image_name(i);
>     		if ( strstr(name, "DynamicLibraries") != NULL ) {
>     			PASS("always-libSystem");
>     			return 1;
>     		}
>     	}
>     
>     	FAIL("always-libSystem");
>     	return 0;
>     }
>     
>     ```
>
>   * app crashes when libSystem cannot be found
>
>     ```
>     int main() 
>     {
>     	// see if libSystem is in list of images
>     	uint32_t count = _dyld_image_count();
>     	for(uint32_t i=0; i < count; ++i) {
>     		const char*  name = _dyld_get_image_name(i);
>     		if ( strstr(name, "/libSystem.") != NULL ) {
>     			PASS("always-libSystem");
>     			return 0;
>     		}
>     	}
>     
>     	FAIL("always-libSystem");
>     	return 0;
>     }
>     
>     ```
>



#  `hook 检测`CheckHook



根据不同的方法定制不同的检查方法



> * Method Swizzling: 原理是替换imp,通过`dladdr`函数得到imp地址所在的模块`info.dli_fname`，如果不是主二进制模块，就可以认定是被hook 了
>
>   ```
>   bool CheckHookForOC(const char* clsname,const char* selname){
>       Dl_info info;
>       SEL sel = sel_registerName(selname);
>       Class cls = objc_getClass(clsname);
>       Method method = class_getInstanceMethod(cls, sel);
>       if(!method){
>           method = class_getClassMethod(cls, sel);
>       }
>       IMP imp = method_getImplementation(method);
>       
>       if(!dladdr((void*)imp, &info)){
>           return false;
>       }
>       
>       printf("%s\n", info.dli_fname);
>       
>       if(!strncmp(info.dli_fname, "/System/Library/Frameworks", 26)){
>           return false;
>       }
>       
>       if(!strcmp(info.dli_fname, _dyld_get_image_name(0))){
>           return false;
>       }
>       
>       return true;
>   }
>   
>   ```
>
> * 符号表替换: 因为fishhook是基于懒加载符号表和非懒加载符号表进行替换的，因此通过遍历符号表中的指针就可以判断app是否被hook 了。
>
>   * 非懒加载的指针指向真实的地址，而懒加载的指针在没有解析到真实的地址之前指向`__stub_helper`;so ，遍历符号表中的每一个指针，然后判断指针是不是指向`__stub_helper`或者系统模块，就可以判断是否被hook。
>     * 结合fishhook代码以及macho文件结构的基础分析，结合`dladdr`函数来实现代码。
>     * `dladdr查看函数的内存空间信息, 验证函数的指针来自程序, 还是苹果的库, 还是未知.`
>
> * inline hook: 替换函数内部的前几条指令跳转
>
>   * cydia substrate: `通过inline hook的方式修改目标函数内存中的汇编指令，使其调转到自己的代码块，以达到修改程序的目的，同时支持method swizzle
>   * 检测方法： 分析函数的内存前几条指令中有没有跳转来判断是不是inline hook。
>     * 如果hook 替换某条指令或者patch内存中的一些指令，这个时候采用“计算代码的哈希值进行验证”
>



# 完整性校验



从文件完整性（检查文件load command 的修改）、是否重签名、bid 等方面入手验证是否程序被修改



#### load command

> * [获取加载的动态库](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/MachOParser/MachOParser/MachOParser.mm): 读取load command  中的 LC_LOAD_DYLIB、LC_LOAD_WEAK_DYLIB信息
>
>
>
>
>
>   ```
>   
>   //获取加载的动态库
>   NSArray* MachOParser::find_load_dylib(){
>       NSMutableArray* array = [[NSMutableArray alloc] init];;
>       mach_header_t *header = (mach_header_t*)base;
>       segment_command_t *cur_seg_cmd;
>       uintptr_t cur = (uintptr_t)this->base + sizeof(mach_header_t);
>       for (uint i = 0; i < header->ncmds; i++,cur += cur_seg_cmd->cmdsize) {
>           cur_seg_cmd = (segment_command_t*)cur;
>           if(cur_seg_cmd->cmd == LC_LOAD_DYLIB || cur_seg_cmd->cmd == LC_LOAD_WEAK_DYLIB){
>               dylib_command *dylib = (dylib_command*)cur_seg_cmd;
>               char* name = (char*)((uintptr_t)dylib + dylib->dylib.name.offset);
>               NSString* dylibName = [NSString stringWithUTF8String:name];
>               [array addObject:dylibName];
>           }
>       }
>       return [array copy];
>   }
>   
>   ```
>



#### 代码检测：获取内存中运行代码的md5值

如果内存中的代码被修改了，那么获取的md5的值就会不一样。



> * [获取运行代码md5](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/MachOParser/MachOParser/MachOParser.mm)
>
>   ```
>   NSString* MachOParser::get_text_data_md5(){
>       NSMutableString *result = [NSMutableString string];
>       section_info_t *section = this->find_section("__TEXT", "__text");
>       local_addr startAddr =  section->addr;
>       unsigned char hash[CC_MD5_DIGEST_LENGTH];
>       CC_MD5((const void *)startAddr, (CC_LONG)section->section->size, hash);
>       for (int i = 0; i < CC_MD5_DIGEST_LENGTH; i++) {
>           [result appendFormat:@"%02x",hash[i]];
>       }
>       return [result copy];
>   }
>   
>   ```
>

#### 重签名检测



> * 判断bid
> * 获取embedded.mobileprovision文件信息
> * 从可执行文件的LC_CODE_SIGNATURE 读取信息
>   * [MachOParser.mm](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/MachOParser/MachOParser/MachOParser.mm)
>     * 1.拿到当前的签名文件的签名信息和编译前的签名信息比对







# See Also 

>* [AntiJailbreak.m](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiJailbreak.m)
>* [CaptainHook.h](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/HookDetection/InsertDylib/CaptainHook.h)
>* [simple-ios-antidebugging-and-antiantidebugging/](https://everettjf.github.io/2015/12/28/simple-ios-antidebugging-and-antiantidebugging/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost HookDetection 反注入: 注入检查机制，获取加载的模块名，判断是否都在白名单中;hook 检测：method swizzle、符号表替换、inline hook -t security
> #原来""的参数，需要自己加上""
```

