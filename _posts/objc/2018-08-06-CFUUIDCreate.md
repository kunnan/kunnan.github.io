---
layout: post
title: CFUUIDCreate
date: 2018-08-06
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Create and return a brand new unique identifier
---



# Create and return a brand new unique identifier 



> * Create and return a brand new unique identifier 
>
>   ```
>   //            CFUUIDRef CFUUIDCreate(CFAllocatorRef alloc);
>               /* Create and return a brand new unique identifier */
>   
>   ```
>
>   ```
>               ///            CFUUIDRef uuidRef = CFUUIDCreate(kCFAllocatorDefault);//CF_EXPORT
>   //            CFUUIDRef CFUUIDCreate(CFAllocatorRef alloc);
>               /* Create and return a brand new unique identifier */
>   
>      //         CFStringRef uuidString = CFUUIDCreateString(kCFAllocatorDefault, uuidRef);
>   
>   ```
>
>   

> * CUtility GetRandomUUID 
>
>   ```
>   void * +[CUtility GetRandomUUID](void * self, void * _cmd) {
>       r4 = CFUUIDCreate(0x0);
>       r5 = CFUUIDCreateString(0x0, r4);
>       CFRelease(r4);
>       [r5 autorelease];
>       r0 = r5;
>       return r0;
>   }
>   ```
>
>   

> * uniqueAppInstanceIdentifier 
>
>   
>
>   ```
>   @implementation UIDevice (org_apache_cordova_UIDevice_Extension)
>   
>   - (NSString*)uniqueAppInstanceIdentifier
>   {
>       NSUserDefaults* userDefaults = [NSUserDefaults standardUserDefaults];
>       static NSString* UUID_KEY = @"CDVUUID";
>   
>       NSString* app_uuid = [userDefaults stringForKey:UUID_KEY];
>   
>       if (app_uuid == nil) {
>           CFUUIDRef uuidRef = CFUUIDCreate(kCFAllocatorDefault);
>           CFStringRef uuidString = CFUUIDCreateString(kCFAllocatorDefault, uuidRef);
>   
>           app_uuid = [NSString stringWithString:(__bridge NSString*)uuidString];
>           [userDefaults setObject:app_uuid forKey:UUID_KEY];
>           [userDefaults synchronize];
>   
>           CFRelease(uuidString);
>           CFRelease(uuidRef);
>       }
>   
>       return app_uuid;
>   }
>   
>   @end
>   
>   ```
>
>   



> * ASI : (void)buildMultipartFormDataPostBody 
>   ```
>   	CFUUIDRef uuid = CFUUIDCreate(nil);
>   	NSString *uuidString = [(NSString*)CFUUIDCreateString(nil, uuid) autorelease];
>   	CFRelease(uuid);
>   	NSString *stringBoundary = [NSString stringWithFormat:@"0xKhTmLbOuNdArY-%@",uuidString];
>   	
>   	[self addRequestHeader:@"Content-Type" value:[NSString stringWithFormat:@"multipart/form-data; charset=%@; boundary=%@", charset, stringBoundary]];
>   
>   ```
>
>   

> * YYADD 
>
>   ```
>   + (NSString *)stringWithUUID {
>       CFUUIDRef uuid = CFUUIDCreate(NULL);
>       CFStringRef string = CFUUIDCreateString(NULL, uuid);
>       CFRelease(uuid);
>       return (__bridge_transfer NSString *)string;
>   }
>   
>   ```
>
>   

# See Also 

>* 格式化 
>
>  ```
>   这个只是做格式化  //      2018-08-03 17:01:38.365 WeChat[1623:187564] --[CUtility GetRandomMD5]
>      //        2018-08-03 17:01:38.366 WeChat[1623:187564] ---[CUtility GetRandomUUID]
>    //          2018-08-03 17:01:38.367 WeChat[1623:187564] ---ret:F2A6DE85-C045-41A2-96F7-DFC00275BE69
>  //            2018-08-03 17:01:38.369 WeChat[1623:187564] --ret:F2A6DE85C04541A296F7DFC00275BE69
>  
>  ```
>
>  
>
>  ```
>  void * +[CUtility GetRandomKey](void * self, void * _cmd) {
>      r4 = sp - 0x60;
>      asm { bfc        r4, #0x0, #0x4 };
>      sp = r4;
>      asm { vst1.64    {d8, d9, d10, d11}, [r4, #0x80]! };
>      asm { vst1.64    {d12, d13, d14, d15}, [r4, #0x80] };
>      sp = sp - 0x60;
>      r0 = 0x630;
>      asm { vmov.i32   q8, #0x0 };
>      r0 = (r0 | 0xd70000) + 0x2c21ec0;
>      r4 = 0x0;
>      r5 = sp + 0x48;
>      asm { vst1.64    {d16, d17}, [r5]! };
>      *r5 = r4;
>      _Unwind_SjLj_Register();
>      stack[2020] = objc_retainAutorelease([objc_msgSend(*r2, *r0) retain]);
>      [stack[2020] UTF8String];
>      [stack[2020] lengthOfBytesUsingEncoding:0x4, r3];
>      Md5Hash::DoHash();
>      r4 = [[NSData dataWithBytes:sp + 0x48 length:0x10] retain];
>      [stack[2020] release];
>      Md5Hash::~Md5Hash();
>      [r4 autorelease];
>      _Unwind_SjLj_Unregister();
>      r0 = *___stack_chk_guard - stack[2039];
>      if (r0 == 0x0) {
>              asm { moveq      r0, r4 };
>      }
>      if (CPU_FLAGS & E) {
>              asm { add.weq    r4, sp, #0x60 };
>      }
>      if (CPU_FLAGS & E) {
>              asm { vld1.64eq  {d8, d9, d10, d11}, [r4, #0x80]! };
>      }
>      if (CPU_FLAGS & E) {
>              asm { vld1.64eq  {d12, d13, d14, d15}, [r4, #0x80] };
>      }
>      if (CPU_FLAGS & E) {
>              asm { sub.weq    r4, r7, #0x18 };
>      }
>      if (CPU_FLAGS & E) {
>              asm { moveq      sp, r4 };
>      }
>      if (CPU_FLAGS & E) {
>              asm { pop.weq    {r8, sl, fp} };
>      }
>      if (CPU_FLAGS & E) {
>              return r0;
>      }
>      r0 = __stack_chk_fail();
>      return r0;
>  }
>  ```
>
>  
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost CFUUIDCreate Create and return a brand new unique identifier -t objc
> #原来""的参数，需要自己加上""
```

