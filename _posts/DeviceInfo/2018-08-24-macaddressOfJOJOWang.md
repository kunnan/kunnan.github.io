---
layout: post
title: macaddressOfJOJOWang
date: 2018-08-24
tag: DeviceInfo
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: macaddress
---



# 前言

[hook sysctl.xm](https://github.com/Naville/WTFJH/blob/99a2079afb48f6dcfccf2ef369a3d7c9e7fc1fdd/Hooks/API/sysctl.xm)



> * platform ： 使用capstone 进行hook 动态库libMobileGestalt.dylib，修改设备类型的时候 ，有些属性是失效的。 比如hw.machine，即设备类型iPhone5,2 ；这个时候我们可以直接修改获取信息 对应的方法———原因：`使用sysctlbyname 函数，就比较不容易hook到。`
>
>   ```
>    [<UIDevice: 0x16ac14e0> platform]
>     
>   -[UIDevice getSysInfoByName:hw.machine ]
>    -ret:iPhone5,2
>     
>   
>   ```
>
>   * `UIDevice getSysInfoByName`
>
>     ```
>     void * +[UIDevice getSysInfoByName:](void * self, void * _cmd, char * arg2) {
>         *((sp - 0x14) + 0xfffffffffffffffc) = r8;
>         sysctlbyname(arg2, 0x0, ((sp - 0x14) + 0xfffffffffffffffc - 0x8) + 0x4, 0x0, 0x0);
>         r6 = malloc(stack[2042]);
>         sysctlbyname(arg2, r6, ((sp - 0x14) + 0xfffffffffffffffc - 0x8) + 0x4, 0x0, 0x0);
>         r4 = [objc_msgSend(@class(NSString), @selector(stringWithCString:encoding:)) retain];
>         free(r6);
>         r0 = loc_2ca9c90(r4, @selector(stringWithCString:encoding:));
>         return r0;
>     }
>     
>     ```
>
>   * `pragma mark sysctlbyname utils`
>
>     ```
>     #pragma mark sysctlbyname utils
>     - (NSString *) getSysInfoByName:(char *)typeSpecifier
>     {
>         size_t size;
>         sysctlbyname(typeSpecifier, NULL, &size, NULL, 0);
>           
>         char *answer = malloc(size);
>         sysctlbyname(typeSpecifier, answer, &size, NULL, 0);
>           
>         NSString *results = [NSString stringWithCString:answer encoding: NSUTF8StringEncoding];
>       
>         free(answer);
>         return results;
>     }
>       
>     - (NSString *) platform
>     {
>         return [self getSysInfoByName:"hw.machine"];
>     }
>       
>     
>     ```
>



# `int	sysctlbyname(const char *, void *, size_t *, void *, size_t);`

#### hook 

```objc
#pragma mark - ******** #import <sys/sysctl.h>
//void * +[UIDevice getSysInfoByName:](void * self, void * _cmd, char * arg2) {
int    (*old_sysctlbyname)(const char *, void *, size_t *, void *, size_t);
int    new_sysctlbyname(const char *name, void *oldp, size_t *oldlenp, void *newp, size_t newlen){
    
        NSString* nameStr=[NSString stringWithUTF8String:name];
        int ret=old_sysctlbyname(name,oldp,oldlenp,newp,newlen);
    NSLog(@"this is new_sysctlbyname() name:%@ oldp result :%s new ret:%d",nameStr,oldp,ret);//2018-08-24 16:19:58.856596 WeChat[1353:75559] this is new_sysctlbyname() name:hw.machine oldp result :iPhone6,1 new ret:0
        return ret;
//    char result[1024];
  //  size_t result_len = 1024;
    //if(sysctlbyname([name UTF8String], &result, &result_len, NULL, 0) < 0)
    //[NSString stringWithUTF8String:result]
    
        //return old_sysctlbyname(name,oldp,oldlenp,newp,newlen);
}

```





> * sysctlbyname:`_machineModel = [self getSystemString:@"hw.machine"];`、`[self getSystemNumber:@"sysctl.proc_native" result:&retval]`
>
>   ```
>   - (BOOL)getSystemNumber:(NSString *)name result:(int *)result
>   {
>       size_t len = sizeof(*result);
>       
>       if(!sysctlbyname([name UTF8String], result, &len, NULL, 0))
>           return false;
>       
>       return YES;
>   }
>   
>   - (NSString *)getSystemString:(NSString *)name
>   {
>       char result[1024];
>       size_t result_len = 1024;
>       
>       if(sysctlbyname([name UTF8String], &result, &result_len, NULL, 0) < 0)
>           return nil;
>       
>       return [NSString stringWithUTF8String:result];
>   }
>   
>   ```
>
>   * `_isEmulator`
>
>     ```
>     
>         int retval;
>     
>         // CPU
>         if([self getSystemNumber:@"hw.cputype" result:&retval])
>             _cpuType = retval;
>         else
>             _cpuType = -1;
>         
>         if([self getSystemNumber:@"hw.cpusubtype" result:&retval])
>             _cpuSubType = retval;
>         else
>             _cpuSubType = -1;
>         
>         if([self getSystemNumber:@"hw.physicalcpu_max" result:&retval])
>             _cpuProcessorCount = retval;
>         else
>             _cpuProcessorCount = -1;
>         
>         if([self getSystemNumber:@"hw.logicalcpu_max" result:&retval])
>             _cpuLogicalProcessorCount = retval;
>         else
>             _cpuLogicalProcessorCount = -1;
>         
>     
>     // is Emulator
>         _isEmulator = YES;
>         if([self getSystemNumber:@"sysctl.proc_native" result:&retval])
>         {
>             if(retval == 0)
>                 _isEmulator = YES;
>             else
>                 _isEmulator = NO;
>         }
>     
>     ```
>
>     * [HQADeviceManager.m](https://github.com/zhangkn/honeyqa-iOS/blob/develop/Pod/Classes/HQADeviceManager.m)



> * **cpuCount**
>
>   ```
>   2018-08-24 16:30:26.462378 WeChat[1375:77977] [<UIDevice: 0x1740300c0> cpuCount]
>   2018-08-24 16:30:26.467018 WeChat[1375:77977] -[UIDevice getSysInfo:3 ]
>   
>   ```
>
>   * getSysInfo
>
>     ```
>     int +[UIDevice getSysInfo:](void * self, void * _cmd, unsigned int arg2) {
>         sp = sp - 0x24;
>         r3 = sp + 0xc;
>         r1 = 0x2;
>         asm { strd       r0, r2, [sp, #0x1c + var_C] };
>         asm { strd       r0, r0, [sp, #0x1c + var_1C] };
>         sysctl(sp + 0x10, r1, sp + 0x8, r3, stack[2039], stack[2040]);
>         r0 = stack[2041];
>         r1 = *___stack_chk_guard - stack[2045];
>         if (r1 == 0x0) {
>                 asm { addeq      sp, #0x1c };
>         }
>         if (CPU_FLAGS & E) {
>                 return r0;
>         }
>         r0 = __stack_chk_fail();
>         return r0;
>     }
>     ```
>
>     * macaddressOfJOJOWang 也是依赖sysctl



# code



```
+ (NSString *) macaddressOfJOJOWang{
    
    int                 mib[6];
    size_t              len;
    char                *buf;
    unsigned char       *ptr;
    struct if_msghdr    *ifm;
    struct sockaddr_dl  *sdl;
    
    mib[0] = CTL_NET;
    mib[1] = AF_ROUTE;
    mib[2] = 0;
    mib[3] = AF_LINK;
    mib[4] = NET_RT_IFLIST;
    
    if ((mib[5] = if_nametoindex("en0")) == 0) {
        printf("Error: if_nametoindex error\n");
        return NULL;
    }
    
    if (sysctl(mib, 6, NULL, &len, NULL, 0) < 0) {
        printf("Error: sysctl, take 1\n");
        return NULL;
    }
    
    if ((buf = malloc(len)) == NULL) {
        printf("Could not allocate memory. error!\n");
        return NULL;
    }
    
    if (sysctl(mib, 6, buf, &len, NULL, 0) < 0) {
        printf("Error: sysctl, take 2");
        free(buf);
        return NULL;
    }
    
    ifm = (struct if_msghdr *)buf;
    sdl = (struct sockaddr_dl *)(ifm + 1);
    ptr = (unsigned char *)LLADDR(sdl);
    NSString *outstring = [NSString stringWithFormat:@"%02X%02X%02X%02X%02X%02X",
                           *ptr, *(ptr+1), *(ptr+2), *(ptr+3), *(ptr+4), *(ptr+5)];
    free(buf);
    
    return outstring;
}

+ (NSString * )macString{
    int mib[6];
    size_t len;
    char *buf;
    unsigned char *ptr;
    struct if_msghdr *ifm;
    struct sockaddr_dl *sdl;
    
    mib[0] = CTL_NET;
    mib[1] = AF_ROUTE;
    mib[2] = 0;
    mib[3] = AF_LINK;
    mib[4] = NET_RT_IFLIST;
    
    if ((mib[5] = if_nametoindex("en0")) == 0) {
        printf("Error: if_nametoindex error\n");
        return NULL;
    }
    
    if (sysctl(mib, 6, NULL, &len, NULL, 0) < 0) {
        printf("Error: sysctl, take 1\n");
        return NULL;
    }
    
    if ((buf = malloc(len)) == NULL) {
        printf("Could not allocate memory. error!\n");
        return NULL;
    }
    
    if (sysctl(mib, 6, buf, &len, NULL, 0) < 0) {
        printf("Error: sysctl, take 2");
        free(buf);
        return NULL;
    }
    
    ifm = (struct if_msghdr *)buf;
    sdl = (struct sockaddr_dl *)(ifm + 1);
    ptr = (unsigned char *)LLADDR(sdl);
    NSString *macString = [NSString stringWithFormat:@"%02X:%02X:%02X:%02X:%02X:%02X",
                           *ptr, *(ptr+1), *(ptr+2), *(ptr+3), *(ptr+4), *(ptr+5)];
    free(buf);
    
    return macString;
}

```



#### 伪代码



> * `MidasIAPCommonUtility`
>
>   ```
>   void * +[MidasIAPCommonUtility getMacAddress](void * self, void * _cmd) {
>       sp = sp - 0x48;
>       r0 = 0x27fc09c;
>       r6 = 0x2c75750;
>       asm { vld1.64    {d16, d17}, [r1, #0x80] };
>       r1 = sp + 0x18;
>       r0 = r0 + 0x7aa8d4;
>       r2 = 0x3;
>       r6 = **(r6 + 0x7aa8d0);
>       stack[2042] = r6;
>       asm { vst1.32    {d16, d17}, [r1]! };
>       *r1 = r2;
>       if (loc_e0a128(r0, r1, r2) == 0x0) goto loc_7aa974;
>   
>   loc_7aa8ea:
>       r3 = sp + 0x14;
>       asm { strd       r0, r0, [sp, #0x40 + var_40] };
>       if (loc_e0a124(sp + 0x18, 0x6, 0x0, r3) < 0x0) goto loc_7aa980;
>   
>   loc_7aa900:
>       r4 = sub_e09ec4();
>       if (r4 == 0x0) goto loc_7aa98c;
>   
>   loc_7aa90c:
>       r3 = sp + 0x14;
>       asm { strd       r0, r0, [sp, #0x40 + var_40] };
>       if (loc_e0a124(sp + 0x18, 0x6, r4, r3) >= 0x0) {
>               asm { strd       lr, r5, [sp, #0x40 + var_38] };
>               asm { strd       sb, ip, [sp, #0x40 + var_40] };
>               sub_e09a10();
>       }
>       else {
>               loc_e0a71c(@"Error: %@", @"sysctl msgBuffer failure");
>       }
>       loc_e09dd8(r4);
>       goto loc_7aa9c8;
>   
>   loc_7aa9c8:
>       r0 = r6 - stack[2042];
>       if (r0 == 0x0) {
>               asm { moveq      r0, r5 };
>       }
>       if (CPU_FLAGS & E) {
>               asm { addeq      sp, #0x34 };
>       }
>       if (CPU_FLAGS & E) {
>               return r0;
>       }
>       loc_e09a64();
>       r0 = loc_7aa9e0();
>       return r0;
>   
>   loc_7aa98c:
>       r5 = @"buffer allocation failure";
>       goto loc_7aa996;
>   
>   loc_7aa996:
>       loc_e0a71c(@"Error: %@", r5);
>       goto loc_7aa9c8;
>   
>   loc_7aa980:
>       r5 = @"sysctl mgmtInfoBase failure";
>       goto loc_7aa996;
>   
>   loc_7aa974:
>       r5 = @"if_nametoindex failure";
>       goto loc_7aa996;
>   }
>   ```
>
> * `[QBInfo getMacAddress]`
>
>   ```
>   void * +[QBInfo getMacAddress](void * self, void * _cmd) {
>       r7 = (sp - 0x10) + 0x8;
>       sp = sp - 0x44;
>       r4 = sp + 0x18;
>       r0 = "en0";
>       asm { vld1.32    {d16, d17}, [r1] };
>       asm { vst1.32    {d16, d17}, [r4], r1 };
>       r0 = loc_e0a128(r0, 0x3);
>       r5 = 0x0;
>       *r4 = r0;
>       if (r0 != 0x0) {
>               r0 = sp + 0x18;
>               r3 = sp + 0x14;
>               r1 = 0x6;
>               r2 = 0x0;
>               asm { strd       r5, r5, [sp, #0x3c + var_3C] };
>               if (loc_e0a124(r0, r1, r2, r3) >= 0x0) {
>                       r4 = sub_e09ec4();
>                       r5 = 0x0;
>                       if (r4 != 0x0) {
>                               r0 = sp + 0x18;
>                               r3 = sp + 0x14;
>                               r1 = 0x6;
>                               r2 = r4;
>                               asm { strd       r5, r5, [sp, #0x3c + var_3C] };
>                               if (loc_e0a124(r0, r1, r2, r3) > 0xffffffff) {
>                                       asm { strd       lr, r5, [sp, #0x3c + var_34] };
>                                       asm { strd       sb, ip, [sp, #0x3c + var_3C] };
>                                       sub_e09a10();
>                                       r5 = loc_e09a18();
>                                       loc_e09dd8(r4);
>                               }
>                               else {
>                                       loc_e09dd8(r4);
>                                       r5 = 0x0;
>                               }
>                       }
>               }
>       }
>       r0 = *___stack_chk_guard - *___stack_chk_guard;
>       if (r0 != 0x0) {
>               loc_e09a64();
>       }
>       r0 = loc_e09a24(r5);
>       return r0;
>   }
>   ```
>
> * uidevice
>
>   ```
>   void * -[UIDevice macaddress](void * self, void * _cmd) {
>       sp = sp - 0x44;
>       r4 = sp + 0x18;
>       r0 = "en0";
>       asm { vld1.32    {d16, d17}, [r1] };
>       asm { vst1.32    {d16, d17}, [r4], r1 };
>       r0 = loc_e0a128(r0, 0x3);
>       *r4 = r0;
>       if (r0 != 0x0) {
>               r3 = sp + 0x14;
>               asm { strd       r0, r0, [sp, #0x3c + var_3C] };
>               if (loc_e0a124(sp + 0x18, 0x6, 0x0, r3) > 0xffffffff) {
>                       r4 = sub_e09ec4();
>                       if (r4 != 0x0) {
>                               r3 = sp + 0x14;
>                               asm { strd       r0, r0, [sp, #0x3c + var_3C] };
>                               if (loc_e0a124(sp + 0x18, 0x6, r4, r3) > 0xffffffff) {
>                                       asm { strd       lr, r5, [sp, #0x3c + var_34] };
>                                       asm { strd       sb, ip, [sp, #0x3c + var_3C] };
>                                       sub_e09a10();
>                                       loc_e09dd8(r4);
>                               }
>                               else {
>                                       loc_e0a120("Error: sysctl, take 2");
>                                       loc_e09dd8(r4);
>                               }
>                       }
>                       else {
>                               loc_e09ef4();
>                       }
>               }
>               else {
>                       loc_e09ef4();
>               }
>       }
>       else {
>               loc_e09ef4();
>       }
>       goto loc_73137c;
>   
>   loc_73137c:
>       r0 = *___stack_chk_guard - *___stack_chk_guard;
>       if (r0 == 0x0) {
>               asm { moveq      r0, r5 };
>       }
>       if (CPU_FLAGS & E) {
>               asm { addeq      sp, #0x34 };
>       }
>       if (CPU_FLAGS & E) {
>               return r0;
>       }
>       loc_e09a64();
>       loc_e0a120("Error: sysctl, take 2");
>       loc_e09dd8(r4);
>       goto loc_73137c;
>   }
>   ```
>
> * wx
>
>   ```
>   void * -[WtloginPlatformInfo macaddress](void * self, void * _cmd) {
>       sp = sp - 0x44;
>       r4 = sp + 0x18;
>       r0 = "en0";
>       asm { vld1.32    {d16, d17}, [r1] };
>       asm { vst1.32    {d16, d17}, [r4], r1 };
>       r0 = loc_e0a128(r0, 0x3);
>       *r4 = r0;
>       if (r0 == 0x0) goto loc_2a4f8c;
>   
>   loc_2a4ec4:
>       r3 = sp + 0x14;
>       asm { strd       r0, r0, [sp, #0x3c + var_3C] };
>       if (loc_e0a124(sp + 0x18, 0x6, 0x0, r3) <= 0xffffffff) goto loc_2a4f8c;
>   
>   loc_2a4edc:
>       r4 = sub_e09ec4();
>       if (r4 == 0x0) goto loc_2a4f8c;
>   
>   loc_2a4ee8:
>       r3 = sp + 0x14;
>       asm { strd       r0, r0, [sp, #0x3c + var_3C] };
>       if (loc_e0a124(sp + 0x18, 0x6, r4, r3) <= 0xffffffff) goto loc_2a4fae;
>   
>   loc_2a4f00:
>       asm { strd       lr, r5, [sp, #0x3c + var_34] };
>       asm { strd       sb, ip, [sp, #0x3c + var_3C] };
>       sub_e09a10();
>       loc_e09dd8(r4);
>       r0 = sub_e09a10();
>       goto loc_2a4f92;
>   
>   loc_2a4f92:
>       r1 = *___stack_chk_guard - *___stack_chk_guard;
>       if (r1 == 0x0) {
>               asm { addeq      sp, #0x34 };
>       }
>       if (CPU_FLAGS & E) {
>               return r0;
>       }
>       loc_e09a64();
>       goto loc_2a4fae;
>   
>   loc_2a4fae:
>       loc_e0a120("Error: sysctl, take 2");
>       loc_e09dd8(r4);
>       goto loc_2a4f90;
>   
>   loc_2a4f90:
>       r0 = 0x0;
>       goto loc_2a4f92;
>   
>   loc_2a4f8c:
>       loc_e0a120();
>       goto loc_2a4f90;
>   }
>   ```
>

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost macaddressOfJOJOWang macaddress -t DeviceInfo
> #原来""的参数，需要自己加上""
```

