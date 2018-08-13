---
layout: post
title: capstoneHook64_capstoneHook32
date: 2018-07-16
tag: MGCopy
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: disassemble MGCopyAnswer and locate the subroutine for hooking
---



# capstoneHook64 

####             if (insn[j].id == ARM64_INS_BL){



#### code

```objc
#import <substrate.h>
#import "capstone.h"
static CFStringRef (*old_MGCA)(CFStringRef Key);
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
          /*cs_disasm(csh handle,
          		const uint8_t *code, size_t code_size,
          		uint64_t address,
          		size_t count,
          		cs_insn **insn);*/
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



#### plist

> * plist
>
>   ```
>   <?xml version="1.0" encoding="UTF-8"?>
>   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
>   <plist version="1.0">
>   <dict>
>   	<key>Filter</key>
>   	<dict>
>   		<key>Bundles</key>
>   		<array>
>   			<string>com.apple.UIKit</string>
>   		</array>
>           <key>Executables</key>
>           <array>
>               <string>MobileGestaltHelper</string>
>           </array>
>           <key>Mode</key>
>           <string>Any</string>
>   	</dict>
>   </dict>
>   </plist>
>   ```
>
>   

# capstoneHook32 

####             if (insn[j].id == ARM_INS_BL){ 



# code

> * [MobileGestaltHooking](https://github.com/kunnan/MobileGestaltHooking/blob/master/Tweak/Makefile)
> * [修改设备信息ChangeCode.m](https://github.com/zhangkn/knaso/blob/master/ASO/ChangeCode/ChangeCode.m )

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost capstoneHook64_capstoneHook32 disassemble MGCopyAnswer and locate the subroutine for hooking -t MGCopy
> #原来""的参数，需要自己加上""
```

