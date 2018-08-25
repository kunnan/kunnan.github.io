---
layout: post
title: AntiJailbreak
date: 2018-08-24
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 越狱环境检测
---

# 前言

[AntiJailbreak.m](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiJailbreak.m)



#### hook `#import <mach-o/dyld.h>`

```
extern uint32_t                    _dyld_image_count(void)                              __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern const struct mach_header*   _dyld_get_image_header(uint32_t image_index)         __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern intptr_t                    _dyld_get_image_vmaddr_slide(uint32_t image_index)   __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern const char*                 _dyld_get_image_name(uint32_t image_index)           __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);

```



#  hook `_dyld_get_image_name`

[Naville/WTFJH](https://github.com/Naville/WTFJH) – [dyldAPI.xm](https://github.com/Naville/WTFJH/blob/99a2079afb48f6dcfccf2ef369a3d7c9e7fc1fdd/Hooks/API/dyldAPI.xm)



# code



> * isJailbreak
>
>   ```
>   #import "AntiJailbreak.h"
>   #import <Foundation/Foundation.h>
>   #import <UIKit/UIKit.h>
>   #import <sys/stat.h>
>   #import <sys/sysctl.h>
>   #import <mach-o/dyld.h>
>   #import <dlfcn.h>
>   
>   #define NOTJAIL     4783242
>   
>   enum {
>       // Failed the Jailbreak Check
>       KFJailbroken = 3429542,
>       // Failed the OpenURL Check
>       KFOpenURL = 321,
>       // Failed the Cydia Check
>       KFCydia = 432,
>       // Failed the Inaccessible Files Check
>       KFIFC = 47293,
>       // Failed the plist check
>       KFPlist = 9412,
>       // Failed the Processes Check with Cydia
>       KFProcessesCydia = 10012,
>       // Failed the Processes Check with other Cydia
>       KFProcessesOtherCydia = 42932,
>       // Failed the Processes Check with other other Cydia
>       KFProcessesOtherOCydia = 10013,
>       // Failed the FSTab Check
>       KFFSTab = 9620,
>       // Failed the System() Check
>       KFSystem = 47475,
>       // Failed the Symbolic Link Check
>       KFSymbolic = 34859,
>       // Failed the File Exists Check
>       KFFileExists = 6625,
>   } JailbrokenChecks;
>   
>   // Define the filesystem check
>   #define FILECHECK [NSFileManager defaultManager] fileExistsAtPath:
>   // Define the exe path
>   #define EXEPATH [[NSBundle mainBundle] executablePath]
>   // Define the plist path
>   #define PLISTPATH [[NSBundle mainBundle] infoDictionary]
>   
>   // Jailbreak Check Definitions
>   #define CYDIALOC        @"/Applications/Cydia.app"
>   #define HIDDENFILES     [NSArray arrayWithObjects:@"/Library/MobileSubstrate/MobileSubstrate.dylib", @"/Applications/RockApp.app",@"/Applications/Icy.app",@"/usr/sbin/sshd",@"/usr/bin/sshd",@"/usr/libexec/sftp-server",@"/Applications/WinterBoard.app",@"/Applications/SBSettings.app",@"/Applications/MxTube.app",@"/Applications/IntelliScreen.app",@"/Library/MobileSubstrate/DynamicLibraries/Veency.plist",@"/Library/MobileSubstrate/DynamicLibraries/LiveClock.plist",@"/private/var/lib/apt",@"/private/var/stash",@"/System/Library/LaunchDaemons/com.ikey.bbot.plist",@"/System/Library/LaunchDaemons/com.saurik.Cydia.Startup.plist",@"/private/var/tmp/cydia.log",@"/private/var/lib/cydia", @"/etc/clutch.conf", @"/var/cache/clutch.plist", @"/etc/clutch_cracked.plist", @"/var/cache/clutch_cracked.plist", @"/var/lib/clutch/overdrive.dylib", @"/var/root/Documents/Cracked/", nil]
>   
>   // Cydia Check
>   int cydiaCheck() {
>       @try {
>           // Create a file path string
>           NSString *filePath = CYDIALOC;
>           struct stat s;
>           // Check if it exists
>           if ([[NSFileManager defaultManager] fileExistsAtPath:filePath]) {
>               // It exists
>               return KFCydia;
>           } else if(!stat([filePath UTF8String], &s)){
>               return KFCydia;
>           }else {
>               // It doesn't exist
>               return NOTJAIL;
>           }
>       }
>       @catch (NSException *exception) {
>           // Error, return false
>           return NOTJAIL;
>       }
>   }
>   
>   // Inaccessible Files Check
>   int inaccessibleFilesCheck() {
>       @try {
>           // Run through the array of files
>           for (NSString *key in HIDDENFILES) {
>               struct stat s;
>               // Check if any of the files exist (should return no)
>               if ([[NSFileManager defaultManager] fileExistsAtPath:key]) {
>                   // Jailbroken
>                   return KFIFC;
>               }else if(!stat([key UTF8String], &s)){
>                   return KFIFC;
>               }
>           }
>           
>           // Shouldn't get this far, return jailbroken
>           return NOTJAIL;
>       }
>       @catch (NSException *exception) {
>           // Error, return false
>           return NOTJAIL;
>       }
>   }
>   
>   // Plist Check
>   int plistCheck() {
>       @try {
>           // Define the Executable name
>           NSString *ExeName = EXEPATH;
>           NSDictionary *ipl = PLISTPATH;
>           // Check if the plist exists
>           if ([FILECHECK ExeName] == FALSE || ipl == nil || ipl.count <= 0) {
>               // Executable file can't be found and the plist can't be found...hmmm
>               return KFPlist;
>           } else {
>               // Everything is good
>               return NOTJAIL;
>           }
>       }
>       @catch (NSException *exception) {
>           // Error, return false
>           return NOTJAIL;
>       }
>   }
>   
>   // Symbolic Link available
>   int symbolicLinkCheck() {
>       @try {
>           // See if the Applications folder is a symbolic link
>           struct stat s;
>           if (lstat("/Applications", &s) != 0) {
>               if (s.st_mode & S_IFLNK) {
>                   // Device is jailbroken
>                   return KFSymbolic;
>               } else
>                   // Not jailbroken
>                   return NOTJAIL;
>           } else {
>               // Not jailbroken
>               return NOTJAIL;
>           }
>       }
>       @catch (NSException *exception) {
>           // Not Jailbroken
>           return NOTJAIL;
>       }
>   }
>   
>   // FileSystem working correctly?
>   int filesExistCheck() {
>       @try {
>           // Check if filemanager is working
>           if (![FILECHECK [[NSBundle mainBundle] executablePath]]) {
>               // Jailbroken and trying to hide it
>               return KFFileExists;
>           } else
>               // Not Jailbroken
>               return NOTJAIL;
>       }
>       @catch (NSException *exception) {
>           // Not Jailbroken
>           return NOTJAIL;
>       }
>   }
>   
>   bool isJailbreak(){
>       // Is the device jailbroken?
>   #if TARGET_IPHONE_SIMULATOR
>       // It's the developer
>       return false;
>   #endif
>       
>       // Make an int to monitor how many checks are failed
>       int motzart = 0;
>       
>       // Cydia Check
>       if (cydiaCheck() != NOTJAIL) {
>           // Jailbroken
>           motzart += 3;
>       }
>       
>       // Inaccessible Files Check
>       if (inaccessibleFilesCheck() != NOTJAIL) {
>           // Jailbroken
>           motzart += 2;
>       }
>       
>       // Plist Check
>       if (plistCheck() != NOTJAIL) {
>           // Jailbroken
>           motzart += 2;
>       }
>       
>       // Symbolic Link Check
>       if (symbolicLinkCheck() != NOTJAIL) {
>           // Jailbroken
>           motzart += 2;
>       }
>       
>       // FilesExist Integrity Check
>       if (filesExistCheck() != NOTJAIL) {
>           // Jailbroken
>           motzart += 2;
>       }
>       
>       // Check if the Jailbreak Integer is 3 or more
>       if (motzart >= 3) {
>           // Jailbroken
>           return 1;
>       }
>       return 0;
>   }
>   
>   ```
>

#### myself 

> * `%hook NSFileManager`
>
>   ```
>   %hook NSFileManager
>   //--[<NSFileManager: 0x17400dc70> fileExistsAtPath:/Applications/Cydia.app ]
>   //--[<NSFileManager: 0x17400dc70> getFileSystemRepresentation: maxLength:1026 withPath:/Applications/Cydia.app ]
>   //-[<NSFileManager: 0x17400dc70> fileExistsAtPath:/private/var/lib/apt/ ]
>   //--[<NSFileManager: 0x17400dc70> getFileSystemRepresentation:/Applications/Cydia.app maxLength:1026 withPath:/private/var/lib/apt/ ]
>   - (bool)fileExistsAtPath:(id)arg1{
>       if ([arg1 containsString:@"/var/containers/Bundle/Application"] ||[arg1 containsString:@"/Applications/Cydia.app"]  || [arg1 containsString:@"/private/var/lib/apt/"])
>       {
>           NSLog(@"This Device is definitely not JailBroken!");
>           return NO;
>       }
>       else{
>           return %orig;
>       }
>   }
>   %end
>   
>   ```
>

#### detectJailbroken

![image](https://ws2.sinaimg.cn/large/af39b376ly1fuksbbaxjxj212z0fan11.jpg)

```
- (BOOL)detectJailbroken
{
#if !TARGET_IPHONE_SIMULATOR
    //Apps and System check list
    BOOL isDirectory;
    NSArray *filePathArray = [NSArray arrayWithObjects:
                              @"/Applications/Cydia.app",
                              @"/Applications/FakeCarrier.app",
                              @"/Applications/Icy.app",
                              @"/Applications/IntelliScreen.app",
                              @"/Applications/MxTube.app",
                              @"/Applications/RockApp.app",
                              @"/Applications/SBSettings.app",
                              @"/Applications/WinterBoard.app",
                              @"/private/var/tmp/cydia.log",
                              @"/usr/binsshd",
                              @"/usr/sbinsshd",
                              @"/usr/libexec/sftp-server",
                              @"/System/Library/LaunchDaemons/com.ikey.bbot.plist",
                              @"/System/Library/LaunchDaemons/com.saurik.Cydia.Startup.plist",
                              @"/Library/MobileSubstrate/MobileSubstrate.dylib",
                              @"/var/log/syslog",
                              @"/bin/bash",
                              @"/bin/sh",
                              @"/etc/ssh/sshd_config",
                              @"/usr/libexec/ssh-keysign",
                              nil];
    NSArray *directoryArray =[NSArray arrayWithObjects:
                              @"/private/var/lib/apt/",
                              @"/private/var/lib/cydia/",
                              @"/private/var/mobileLibrary/SBSettingsThemes/",
                              @"/private/var/stash/",
                              @"/usr/libexec/cydia/",
                              @"/var/cache/apt/",
                              @"/var/lib/apt/",
                              @"/var/lib/cydia/",
                              @"/etc/apt/",
                              nil];
    
    for(NSString *existsPath in filePathArray)
        if([[NSFileManager defaultManager] fileExistsAtPath:existsPath])
            return YES;
    
    for(NSString *existsDirectory in directoryArray)
        if([[NSFileManager defaultManager] fileExistsAtPath:existsDirectory isDirectory:&isDirectory])
            return YES;
    
    // SandBox Integrity Check
    int pid = fork();
    if(!pid)
        exit(0);
    
    if(pid >= 0)
        return YES;
    
    // Symbolic link verification
    struct stat s;
    if(lstat("/Applications", &s) ||
       lstat("/var/stash/Library/Ringstones", &s) ||
       lstat("/var/stash/Library/Wallpaper", &s) ||
       lstat("/var/stash/usr/include", &s) ||
       lstat("/var/stash/usr/libexec", &s) ||
       lstat("/var/stash/usr/share", &s) ||
       lstat("/var/stash/usr/arm-apple-darwin9", &s))
    {
        if(s.st_mode & S_IFLNK)
            return YES;
    }
    
    // Try to write file in private
    NSError *error;
    [[NSString stringWithFormat:@"Jailbreak test string"]
     writeToFile:@"/private/test_jb.txt"
     atomically:YES
     encoding:NSUTF8StringEncoding error:&error];
    
    if(!error)
        return YES;
    else
        [[NSFileManager defaultManager] removeItemAtPath:@"/private/test_jb.txt" error:nil];
#endif
    
    return NO;
}

- (BOOL)detectAppCracked
{
#if !TARGET_IPHONE_SIMULATOR
    NSBundle *bundle = [NSBundle mainBundle];
    NSString* bundlePath = [bundle bundlePath];
    NSFileManager *manager = [NSFileManager defaultManager];
    BOOL fileExists;
    
    //Check to see if the app is running on root
    int root = getgid();
    if(root <= 10)
        return YES;
    
    //Checking for identity signature
    char symCipher[] = { '(', 'H', 'Z', '[', '9', '{', '+', 'k', ',', 'o', 'g', 'U', ':', 'D', 'L', '#', 'S', ')', '!', 'F', '^', 'T', 'u', 'd', 'a', '-', 'A', 'f', 'z', ';', 'b', '\'', 'v', 'm', 'B', '0', 'J', 'c', 'W', 't', '*', '|', 'O', '\\', '7', 'E', '@', 'x', '"', 'X', 'V', 'r', 'n', 'Q', 'y', '>', ']', '$', '%', '_', '/', 'P', 'R', 'K', '}', '?', 'I', '8', 'Y', '=', 'N', '3', '.', 's', '<', 'l', '4', 'w', 'j', 'G', '`', '2', 'i', 'C', '6', 'q', 'M', 'p', '1', '5', '&', 'e', 'h' };
    char csignid[] = "V.NwY2*8YwC.C1";
    for(int i = 0; i < strlen(csignid); i ++)
    {
        for(int j = 0; j < sizeof(symCipher); j ++)
        {
            if(csignid[i] == symCipher[j])
            {
                csignid[i] = j + 0x21;
                break;
            }
        }
    }
    NSString* signIdentity = [[NSString alloc] initWithCString:csignid encoding:NSUTF8StringEncoding];
    
    NSDictionary *info = [bundle infoDictionary];
    if([info objectForKey:signIdentity])
        return YES;
    
    // Check if the below .plist files exists in the app bundle
    fileExists = [manager fileExistsAtPath:([NSString stringWithFormat:@"%@/%@", bundlePath, @"_CodeSignature"])];
    if(!fileExists)
        return YES;
    
    fileExists = [manager fileExistsAtPath:([NSString stringWithFormat:@"%@/%@", bundlePath, @"ResourceRules.plist"])];
    if(!fileExists)
        return YES;
    
    
    fileExists = [manager fileExistsAtPath:([NSString stringWithFormat:@"%@/%@", bundlePath, @"SC_Info"])];
    if(!fileExists)
        return YES;
    
    //Check if the info.plist and exectable files have been modified
    NSDate* pkgInfoModifiedDate = [[manager attributesOfItemAtPath:[[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"PkgInfo"] error:nil] fileModificationDate];
    
    NSString* infoPath = [NSString stringWithFormat:@"%@/%@", bundlePath, @"Info.plist"];
    NSDate* infoModifiedDate = [[manager attributesOfItemAtPath:infoPath error:nil] fileModificationDate];
    if([infoModifiedDate timeIntervalSinceReferenceDate] > [pkgInfoModifiedDate timeIntervalSinceReferenceDate])
        return YES;
    
    NSString* appPathName = [NSString stringWithFormat:@"%@/%@", bundlePath, [[bundle infoDictionary] objectForKey:@"CFBundleDisplayName"]];
    NSDate* appPathNameModifiedDate = [[manager attributesOfItemAtPath:appPathName error:nil] fileModificationDate];
    if([appPathNameModifiedDate timeIntervalSinceReferenceDate] > [pkgInfoModifiedDate timeIntervalSinceReferenceDate])
        return YES;
#endif
    
    return NO;
}

```



#### wl

```
+ (BOOL)mgjpf_isJailbroken
{
    //以下检测的过程是越往下，越狱越高级
    
    //    /Applications/Cydia.app, /privte/var/stash
    BOOL jailbroken = NO;
    NSString *cydiaPath = @"/Applications/Cydia.app";
    NSString *aptPath = @"/private/var/lib/apt/";
    if ([[NSFileManager defaultManager] fileExistsAtPath:cydiaPath]) {
        jailbroken = YES;
    }
    if ([[NSFileManager defaultManager] fileExistsAtPath:aptPath]) {
        jailbroken = YES;
    }
    
    //可能存在hook了NSFileManager方法，此处用底层C stat去检测
    struct stat stat_info;
    if (0 == stat("/Library/MobileSubstrate/MobileSubstrate.dylib", &stat_info)) {
        jailbroken = YES;
    }
    if (0 == stat("/Applications/Cydia.app", &stat_info)) {
        jailbroken = YES;
    }
    if (0 == stat("/var/lib/cydia/", &stat_info)) {
        jailbroken = YES;
    }
    if (0 == stat("/var/cache/apt", &stat_info)) {
        jailbroken = YES;
    }
    //    /Library/MobileSubstrate/MobileSubstrate.dylib 最重要的越狱文件，几乎所有的越狱机都会安装MobileSubstrate
    //    /Applications/Cydia.app/ /var/lib/cydia/绝大多数越狱机都会安装
    //    /var/cache/apt /var/lib/apt /etc/apt
    //    /bin/bash /bin/sh
    //    /usr/sbin/sshd /usr/libexec/ssh-keysign /etc/ssh/sshd_config
    
    //可能存在stat也被hook了，可以看stat是不是出自系统库，有没有被攻击者换掉
    //这种情况出现的可能性很小
    int ret;
    Dl_info dylib_info;
    int (*func_stat)(const char *,struct stat *) = stat;
    if ((ret = dladdr(func_stat, &dylib_info))) {
        //        NSLog(@"lib:%s",dylib_info.dli_fname);      //如果不是系统库，肯定被攻击了
        if (strcmp(dylib_info.dli_fname, "/usr/lib/system/libsystem_kernel.dylib")) {   //不相等，肯定被攻击了，相等为0
            jailbroken = YES;
        }
    }
    
    //还可以检测链接动态库，看下是否被链接了异常动态库，但是此方法存在appStore审核不通过的情况，这里不作罗列
    //通常，越狱机的输出结果会包含字符串： Library/MobileSubstrate/MobileSubstrate.dylib——之所以用检测链接动态库的方法，是可能存在前面的方法被hook的情况。这个字符串，前面的stat已经做了
    
    //如果攻击者给MobileSubstrate改名，但是原理都是通过DYLD_INSERT_LIBRARIES注入动态库
    //那么可以，检测当前程序运行的环境变量
    char *env = getenv("DYLD_INSERT_LIBRARIES");
    if (env != NULL) {
        jailbroken = YES;
    }
    
    return jailbroken;
}

```



#### wx



> * `char +[JailBreakHelper JailBroken](void * self, void * _cmd) {`
>
>   ```
>   char +[JailBreakHelper JailBroken](void * self, void * _cmd) {
>       asm { bfc        r4, #0x0, #0x3 };
>       r0 = loc_2bc06a0(@class(NSArray), @selector(arrayWithObjects:), 0xb2e6, @"/usr/libexec/ssh-keysign", stack[2048], stack[2049]);
>       return r0;
>   }
>   ```
>
> * `char -[JailBreakHelper IsJailBreak](void * self, void * _cmd) {`
>
>   ```
>   har -[JailBreakHelper IsJailBreak](void * self, void * _cmd) {
>       r7 = (sp - 0x14) + 0xc;
>       r4 = sp - 0xb8;
>       asm { bfc        r4, #0x0, #0x3 };
>       sp = r4;
>       r1 = @selector(arrayWithObjects:);
>       r0 = @class(NSArray);
>       stack[2008] = 0x0;
>       stack[2007] = @"/etc/ssh/sshd_config";
>       stack[2006] = @"/usr/libexec/ssh-keysign";
>       r2 = @"/Library/MobileSubstrate/MobileSubstrate.dylib";
>       stack[2005] = @"/usr/sbin/sshd";
>       r3 = @"/Applications/Cydia.app/";
>       stack[2004] = @"/bin/sh";
>       asm { strd       r4, r6, [sp, #0xb0 + var_B0] };
>       r0 = objc_msgSend(r0, r1, r2, r3, stack[2002], stack[2003], stack[2004], stack[2005], stack[2006], stack[2007], stack[2008], stack[2009], stack[2010], stack[2011], stack[2012], stack[2013], stack[2014], stack[2015], stack[2016], stack[2017], stack[2018], stack[2019], stack[2020], stack[2021], stack[2022], stack[2023], stack[2024], stack[2025], stack[2026], stack[2027]);
>       r7 = r7;
>       r0 = [r0 retain];
>       r5 = sp + 0x30;
>       asm { vmov.i32   q8, #0x0 };
>       asm { vst1.64    {d16, d17}, [r1]! };
>       asm { vst1.64    {d16, d17}, [r1] };
>       r4 = [r0 retain];
>       r0 = [r4 countByEnumeratingWithState:r5 objects:sp + 0x54 count:0x10, stack[2003], stack[2004], stack[2005], stack[2006]];
>       stack[2013] = r0;
>       if (r0 == 0x0) goto loc_2bc0960;
>   
>   loc_2bc08c4:
>       stack[2012] = *stack[2016];
>       stack[2011] = r4;
>       goto loc_2bc08e0;
>   
>   loc_2bc08e0:
>       r5 = *@selector(fileExistsAtPath:);
>       r6 = 0x0;
>       r8 = *@selector(defaultManager);
>       goto loc_2bc08e8;
>   
>   loc_2bc08e8:
>       r0 = *stack[2016];
>       if (r0 != stack[2012]) {
>               asm { ldrne      r0, [sp, #0xb0 + var_8C] };
>       }
>       if (CPU_FLAGS & NE) {
>               objc_enumerationMutation(r0);
>       }
>       r0 = objc_msgSend(@class(NSFileManager), r8);
>       r7 = r7;
>       r0 = [r0 retain];
>       r4 = objc_msgSend(r0, r5);
>       [r0 release];
>       if (r4 != 0x0) goto loc_2bc096a;
>   
>   loc_2bc092a:
>       r6 = r6 + 0x1;
>       if (r6 < stack[2013]) goto loc_2bc08e8;
>   
>   loc_2bc0932:
>       r4 = stack[2011];
>       r0 = [r4 countByEnumeratingWithState:sp + 0x30 objects:sp + 0x54 count:0x10, stack[2003], stack[2004], stack[2005], stack[2006]];
>       stack[2013] = r0;
>       if (r0 != 0x0) goto loc_2bc08e0;
>   
>   loc_2bc0960:
>       [r4 release];
>       goto loc_2bc0974;
>   
>   loc_2bc0974:
>       [r4 release];
>       r0 = *___stack_chk_guard - *___stack_chk_guard;
>       if (r0 == 0x0) {
>               asm { moveq      r0, r5 };
>       }
>       if (CPU_FLAGS & E) {
>               asm { sub.weq    r4, r7, #0x18 };
>       }
>       if (CPU_FLAGS & E) {
>               asm { moveq      sp, r4 };
>       }
>       if (CPU_FLAGS & E) {
>               asm { pop.weq    {r8, sl, fp} };
>       }
>       if (CPU_FLAGS & E) {
>               return r0;
>       }
>       r0 = __stack_chk_fail();
>       return r0;
>   
>   loc_2bc096a:
>       r4 = stack[2011];
>       [r4 release];
>       goto loc_2bc0974;
>   }
>   
>   ```
>

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost AntiJailbreak 越狱环境检测 -t security
> #原来""的参数，需要自己加上""
```

