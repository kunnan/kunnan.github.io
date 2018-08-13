---
layout: post
title: UUIDString
date: 2018-07-31
tag: 
- UUID
- MGCopy
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 设备udid的收集
---



# 前言

> * 另外，我发现，普通app如果使用sysctlbyname 函数进行设备信息的时候，使用 capstone 进行hook libMobileGestalt的方法，有些属性是失效的。 比如hw.machine，即设备类型iPhone5,2 这个时候，你就要针对app的获取信息对应的方法进行处理 
>   * `-[UIDevice getSysInfoByName:hw.machine ]  `,[具体的情况请看这里](https://kunnan.github.io/2018/07/31/UUIDString/#uidevice)
> * 很多设备的udid 都是基于CFUUIDCreate、CFUUIDCreateString 进行创建
>   * OpenUDID  采用CFUUIDCreate、CFUUIDCreateString 进行创建 

# UUIDString

> * app自己生成udid保存到钥匙串
>
>   ```
>    -[<SafeSingleItemWrapper: 0x16212720> initSafeWithIdentifier:Ksid accessGroup:unknown ]
>   
>   ```
>
>   ```
>    --[<SingleItemWrapper: 0x16212570> initWithIdentifier:Ksid accessGroup:unknown ]
>   
>   ```
>
>   ```
>    [MMKeychain load:wx.dat accessGroup:unknown migratable:0 ]
>     [MMKeychain load:com.tencent.xin.updateresinfo accessGroup:teamid.com.tencent.xin migratable:1 ]
>   
>   ```
>
>   
>
>   ```
>   - (NSString *)strUUID{
>   
>       if (_strUUID == nil || [_strUUID isEqualToString:@""]) {
>   
>           
>   
>           KNKeychainItemWrapper *wrapper = [[KNchainItemWrapper alloc] initWithIdentifier:@"wkneiknlkniu"accessGroup:nil];
>   
>           // 读测试
>   
>           NSString *strMD5 = [wrapper  objectForKey:(__bridge id)kSecAttrAccount];
>   
>   
>   
>           NSLog(@"读出md5:%@",strMD5);
>   
>           if (strMD5 == nil || [strMD5 isEqualToString:@""])
>   
>           {
>   
>               strMD5 = [MD5Generator MD5];
>   
>               [wrapper setObject:strMD5 forKey:(__bridge id)kSecAttrAccount];
>   
>               
>   
>   
>   
>               NSLog(@"写入MD5:%@",strMD5);
>   
>           }
>   
>           _strUUID = strMD5;
>   
>           NSLog(@"strUUID:%@", strMD5);
>   
>       }
>   
>       
>   
>       return _strUUID;
>   
>   }
>   
>   
>   ```
>
>   



> * _idfa
>
>   ```
>           _idfa = [ASIdentifierManager sharedManager].advertisingIdentifier.UUIDString;
>   
>   ```
>
>   
>
> * _idfv
>
>   ```
>           _idfv = [UIDevice currentDevice].identifierForVendor.UUIDString;
>   
>   ```
>
>   



#### ASIdentifierManager 

> *  [<ASIdentifierManager: 0x16f52170> advertisingIdentifier] 
>
>   ```
>    ret:<__NSConcreteUUID 0x180b4bf0> 6944A2EB-ED8F-4519-9D09-ABE2E015EDD8
>   
>   ```
>
>   
>
> *  [<ASIdentifierManager: 0x16f52170> isAdvertisingTrackingEnabled] 
>
>   ```
>    ret:1
>   
>   ```
>
>   
>
> *  [ASIdentifierManager sharedManager] 
>
>   ```
>    ret:<ASIdentifierManager: 0x16f52170>
>   
>   ```
>
>   
>
> * ASIdentifierManager 
>
>   
>
>   ```
>   @class NSUUID;
>   
>   @interface ASIdentifierManager : NSObject
>   
>   @property (nonatomic,readonly) NSUUID * advertisingIdentifier; 
>   @property (getter=isAdvertisingTrackingEnabled,nonatomic,readonly) char advertisingTrackingEnabled; 
>   +(id)sharedManager;
>   -(NSUUID *)advertisingIdentifier;
>   -(char)isAdvertisingTrackingEnabled;
>   @end
>   
>   
>   ```
>
>   

# openUDID

####  // openUDID Usage:

> * value
>
>   
>
>   ```
>   //    #include "OpenUDID.h"
>   
>   //    NSString* openUDID = [OpenUDID value];
>   ```
>
>   * save to Pasteboard: 往粘贴板存储用户\设备信息
>
>     
>
>     ```
>     - (void)setModel:(WLUserModel *)model{
>         _model = model;
>         
>         if (model) {
>             
>             
>             UIPasteboard *pasteboard = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeyUserID create:YES];
>             pasteboard.persistent = YES;
>             pasteboard.string = model.UserId;
>             
>             UIPasteboard *pasteboardSign = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeySign create:YES];
>             pasteboardSign.persistent = YES;
>             pasteboardSign.string = model.Sign;
>         }
>     }
>     
>     ```
>
>     ```
>     - (WLUserModel *)model{
>         
>         if (_model == nil) {
>             
>             
>             
>             /**
>              
>              
>              应用级别的，数据在属于自己的应用内部共享；
>              (默认情况下是不会把数据写进沙盒的，也就是说（复制、剪切）粘贴内容会因为应用的退出而销毁掉，我们可以设置相关属性 persistent值为 YES让其进行数据的持久化存储起来)
>              
>              
>              Ps：其他的一些属性，我们可以点进去观察一下基本可以理解例如 persistent 是否进行数据持久化 还有 changeCount 改变次数（剪切板）系统重启方才重新计数
>              
>              */
>             NSString *contentSign =@"";
>             NSString *contentUserID =@"";
>             UIPasteboard *pasteboard = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeySign create:NO];
>             
>             if (pasteboard){
>                 
>                 contentSign = pasteboard.string;
>                 
>                 
>             }
>             UIPasteboard *pasteboardUserID = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeyUserID create:NO];
>             
>             if (pasteboardUserID){
>                 
>                 contentUserID = pasteboardUserID.string;
>                 
>                 
>             }
>           
>             _model = [[WLUserModel alloc]init];
>             _model.UserId =contentUserID;
>             _model.Sign = contentSign;
>             
>             
>         }
>         return _model;
>     }
>     
>     ```
>
>     
>
>   * save to Keychain 
>
>     
>
>     ```
>             // 读测试
>             NSString *openUDID = [wrapper  objectForKey:(__bridge id)kSecValueData];
>                     [wrapper setObject:openUDID forKey:(__bridge id)kSecValueData];
>     
>     ```
>
>     



# 数据的保存方式

文件、钥匙串、剪切板、NSUserDefaults

#  import <sys/utsname.h>

> * utsname
>
>   ```
>   #import <sys/utsname.h>
>   ...
>   
>   struct utsname systemInfo;
>   uname(&systemInfo);
>   
>   NSString *deviceName = [NSString stringWithCString:systemInfo.machine encoding:NSUTF8StringEncoding];
>   ...
>   
>   ```
>
>   

> * [UIDevice _graphicsQuality](https://stackoverflow.com/questions/27878769/check-if-device-supports-blur#27879304)
>
>   ```
>   UIDevice has an internal method [UIDevice _graphicsQuality] that seems promising, but of course your app will be rejected by Apple. Let's create our own method:
>   
>   NSSet *graphicsQuality = [NSSet setWithObjects:@"iPad",
>                                                        @"iPad1,1",
>                                                        @"iPhone1,1",
>                                                        @"iPhone1,2",
>                                                        @"iPhone2,1",
>                                                        @"iPhone3,1",
>                                                        @"iPhone3,2",
>                                                        @"iPhone3,3",
>                                                        @"iPod1,1",
>                                                        @"iPod2,1",
>                                                        @"iPod2,2",
>                                                        @"iPod3,1",
>                                                        @"iPod4,1",
>                                                        @"iPad2,1",
>                                                        @"iPad2,2",
>                                                        @"iPad2,3",
>                                                        @"iPad2,4",
>                                                        @"iPad3,1",
>                                                        @"iPad3,2",
>                                                        @"iPad3,3",
>                                                        nil];
>    if ([graphicsQuality containsObject:deviceName()]) {
>        // Device with poor graphics, blur not supported
>    } else {
>        // Blur supported
>    }
>   
>   ```
>
>   * [[Detect if device properly displays UIVisualEffectView?](https://stackoverflow.com/questions/26411227/detect-if-device-properly-displays-uivisualeffectview)](https://stackoverflow.com/questions/26411227/detect-if-device-properly-displays-uivisualeffectview)

# UIDevice 

很多的信息可以从`UIDevice `进行获取，例如

> 
>
> * 通过UIDevice获取电池用量和状态转换。
>
>    ```
>    [<UIDevice: 0x16ac14e0> isBatteryMonitoringEnabled]
>    ret:0
>    [<UIDevice: 0x16ac14e0> setBatteryMonitoringEnabled:1 ]
>    -[<UIDevice: 0x16ac14e0> batteryState]
>    -ret:0
>    -[<UIDevice: 0x16ac14e0> _setBatteryLevel:1 ]
>    -[<UIDevice: 0x16ac14e0> _setBatteryState:3 ]
>     
>    ```
>
>   ```
>       device.batteryMonitoringEnabled = YES;
>   
>       //开启了监视电池状态的功能
>   
>   
>   
>       [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(batteryLevelChanged:) name:@"UIDeviceBatteryLevelDidChangeNotification" object:device];
>   
>       [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(batteryStateChanged:) name:@"UIDeviceBatteryStateDidChangeNotification" object:device];
>   
>   
>   
>   - (void)batteryLevelChanged:(id)sender
>   
>   {
>   
>   
>       UIDevice *myDevice = [UIDevice currentDevice];
>   
>       [myDevice setBatteryMonitoringEnabled:YES];
>   
>       float batteryLevel = [myDevice batteryLevel];
>   
>       NSLog(@"电池剩余比例：%@", [NSString stringWithFormat:@"%f",batteryLevel*100]);
>   
>   
>   }
>   
>   
>   
>   -(void)batteryStateChanged:(id)sender
>   
>   {
>   
>       NSArray *stateArray = [NSArray arrayWithObjects:@"未开启监视电池状态",@"电池未充电状态",@"电池充电状态",@"电池充电完成",nil];
>   
>       NSLog(@"电池状态：%@", [stateArray objectAtIndex:[[UIDevice currentDevice] batteryState]]);
>   
>   }
>   ```
>
>   
>
> * getSysInfo 
>
>   
>
>   ```
>   int +[UIDevice getSysInfo:](void * self, void * _cmd, unsigned int arg2) {
>       sp = sp - 0x24;
>       r3 = sp + 0xc;
>       r1 = 0x2;
>       asm { strd       r0, r2, [sp, #0x1c + var_C] };
>       asm { strd       r0, r0, [sp, #0x1c + var_1C] };
>       sysctl(sp + 0x10, r1, sp + 0x8, r3, stack[2039], stack[2040]);
>       r0 = stack[2041];
>       r1 = *___stack_chk_guard - stack[2045];
>       if (r1 == 0x0) {
>               asm { addeq      sp, #0x1c };
>       }
>       if (CPU_FLAGS & E) {
>               return r0;
>       }
>       r0 = __stack_chk_fail();
>       return r0;
>   }
>   ```
>
>   ```
>   #pragma mark sysctl utils
>   - (NSUInteger) getSysInfo: (uint) typeSpecifier
>   {
>       size_t size = sizeof(int);
>       int results;
>       int mib[2] = {CTL_HW, typeSpecifier};
>       sysctl(mib, 2, &results, &size, NULL, 0);
>       return (NSUInteger) results;
>   }
>   
>   - (NSUInteger) cpuFrequency
>   {
>       return [self getSysInfo:HW_CPU_FREQ];
>   }
>   
>   - (NSUInteger) busFrequency
>   {
>       return [self getSysInfo:HW_BUS_FREQ];
>   }
>   
>   - (NSUInteger) cpuCount
>   {
>       return [self getSysInfo:HW_NCPU];
>   }
>   
>   - (NSUInteger) totalMemory
>   {
>       return [self getSysInfo:HW_PHYSMEM];
>   }
>   
>   - (NSUInteger) userMemory
>   {
>       return [self getSysInfo:HW_USERMEM];
>   }
>   
>   - (NSUInteger) maxSocketBufferSize
>   {
>       return [self getSysInfo:KIPC_MAXSOCKBUF];
>   }
>   
>   ```
>
>   ```
>    [<UIDevice: 0x16ac14e0> cpuCount]
>    -[UIDevice getSysInfo:3 ]
>    -ret:2
>    ret:2
>   #define	HW_NCPU		 3		/* int: number of cpus */
>   
>   ```
>
>   * CTL_HW
>
>     ```
>     /*
>      * CTL_HW identifiers
>      */
>     #define	HW_MACHINE	 1		/* string: machine class */
>     #define	HW_MODEL	 2		/* string: specific machine model */
>     #define	HW_NCPU		 3		/* int: number of cpus */
>     #define	HW_BYTEORDER	 4		/* int: machine byte order */
>     #define	HW_PHYSMEM	 5		/* int: total memory */
>     #define	HW_USERMEM	 6		/* int: non-kernel memory */
>     #define	HW_PAGESIZE	 7		/* int: software page size */
>     #define	HW_DISKNAMES	 8		/* strings: disk drive names */
>     #define	HW_DISKSTATS	 9		/* struct: diskstats[] */
>     #define	HW_EPOCH  	10		/* int: 0 for Legacy, else NewWorld */
>     #define HW_FLOATINGPT	11		/* int: has HW floating point? */
>     #define HW_MACHINE_ARCH	12		/* string: machine architecture */
>     #define HW_VECTORUNIT	13		/* int: has HW vector unit? */
>     #define HW_BUS_FREQ	14		/* int: Bus Frequency */
>     #define HW_CPU_FREQ	15		/* int: CPU Frequency */
>     #define HW_CACHELINE	16		/* int: Cache Line Size in Bytes */
>     #define HW_L1ICACHESIZE	17		/* int: L1 I Cache Size in Bytes */
>     #define HW_L1DCACHESIZE	18		/* int: L1 D Cache Size in Bytes */
>     #define HW_L2SETTINGS	19		/* int: L2 Cache Settings */
>     #define HW_L2CACHESIZE	20		/* int: L2 Cache Size in Bytes */
>     #define HW_L3SETTINGS	21		/* int: L3 Cache Settings */
>     #define HW_L3CACHESIZE	22		/* int: L3 Cache Size in Bytes */
>     #define HW_TB_FREQ	23		/* int: Bus Frequency */
>     #define HW_MEMSIZE	24		/* uint64_t: physical ram size */
>     #define HW_AVAILCPU	25		/* int: number of available CPUs */
>     #define	HW_MAXID	26		/* number of valid hw ids */
>     
>     ```
>
>     
>
> * name 
>
>   ```
>    [1;36m[knFake] [m[0;36m/Users/devzkn/code/tweak/knwx2018/knFake/knFake/Hook.mm:180[m [0;30;46mDEBUG:[m UserAssignedDeviceName: iPhone => (null)
>    ret:iPhone
>   
>   ```
>
>   
>
> * iba_identifier 
>
>   ```
>    [<UIDevice: 0x16ac14e0> iba_identifier]
>    -[<UIDevice: 0x16ac14e0> iba_isSimulator]
>     -ret:0
>      -[<UIDevice: 0x16ac14e0> identifierForVendor]//_idfv
>       -ret:<__NSConcreteUUID 0x16f68c30> 99474A61-C43F-4D65-B3AF-B18A48F33EC9
>    ret:99474A61-C43F-4D65-B3AF-B18A48F33EC9
>   
>   
>   
>   
>   ```
>
>   
>
> * systemName 
>
>   ```
>    [<UIDevice: 0x16ac14e0> systemName]
>    ret:iPhone OS
>   ```
>
>   
>
> * (void)beginGeneratingDeviceOrientationNotifications __TVOS_PROHIBITED;      // nestable 
>
>   ```
>    [<UIDevice: 0x16ac14e0> beginGeneratingDeviceOrientationNotifications]
>   
>   ```
>
>   
>
> * _graphicsQuality 
>
>   
>
>   ```
>    [<UIDevice: 0x16ac14e0> _graphicsQuality]
>    ret:100
>   ```
>
>   
>
> * NG_DEVICE_SINGLENAME  设备所属国家
>
>   
>
>   
>
>   ```
>    [1;36m[knFake] [m[0;36m/Users/devzkn/code/tweak/knwx2018/knFake/knFake/Hook.mm:180[m [0;30;46mDEBUG:[m h63QSdBCiT/z0WU6rdQv6Q: J => (null)
>       //设备所属国家
>       if(CFEqual(Key, CFSTR("h63QSdBCiT/z0WU6rdQv6Q")) ){
>           //J
>           if(NG_DEVICE_SINGLENAME){
>               return (CFStringRef)NG_DEVICE_SINGLENAME;
>           }
>   
>       }
>   
>   ```
>
>   
>
> * localizedModel 
>
>   ```
>    [1;36m[knFake] [m[0;36m/Users/devzkn/code/tweak/knwx2018/knFake/knFake/Hook.mm:180[m [0;30;46mDEBUG:[m DeviceName: iPhone => (null)
>    ret:iPhone
>   
>   ```
>
>   
>
> * model : 设备名称
>
>   ```
>    [<UIDevice: 0x16ac14e0> model]
>   [1;36m[knFake] [m[0;36m/Users/devzkn/code/tweak/kn2018/knFake/knFake/Hook.mm:180[m [0;30;46mDEBUG:[m DeviceName: iPhone => (null)
>    ret:iPhone
>   
>   ```
>
>   
>
> * 常量UIUserInterfaceIdiomPhone 用于判断是否为iPhone设备，UIUserInterfaceIdiomPad用于判断是否为iPad设备
>
>   ```
>    [<UIDevice: 0x16ac14e0> userInterfaceIdiom]
>     ret:0
>     typedef NS_ENUM(NSInteger, UIUserInterfaceIdiom) {
>       UIUserInterfaceIdiomUnspecified = -1,
>       UIUserInterfaceIdiomPhone NS_ENUM_AVAILABLE_IOS(3_2), // iPhone and iPod touch style UI
>       UIUserInterfaceIdiomPad NS_ENUM_AVAILABLE_IOS(3_2), // iPad style UI
>       UIUserInterfaceIdiomTV NS_ENUM_AVAILABLE_IOS(9_0), // Apple TV style UI
>       UIUserInterfaceIdiomCarPlay NS_ENUM_AVAILABLE_IOS(9_0), // CarPlay style UI
>   };
>   
>   
>    if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPhone)
>   
>   {
>   
>   // iPhone设备
>   
>   } else
>   
>   {
>   
>   // iPad 设备
>   
>   }
>   
>   
>   ```
>
>   
>
> * platform ： 使用capstone 进行hook 动态库libMobileGestalt.dylib，修改设备类型的时候 ，有些属性是失效的。 比如hw.machine，即设备类型iPhone5,2 ；这个时候我们可以直接修改获取信息 对应的方法---------原因：`使用sysctlbyname 函数，就比较不容易hook到。`
>
>   
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
>   ```
>   void * +[UIDevice getSysInfoByName:](void * self, void * _cmd, char * arg2) {
>       *((sp - 0x14) + 0xfffffffffffffffc) = r8;
>       sysctlbyname(arg2, 0x0, ((sp - 0x14) + 0xfffffffffffffffc - 0x8) + 0x4, 0x0, 0x0);
>       r6 = malloc(stack[2042]);
>       sysctlbyname(arg2, r6, ((sp - 0x14) + 0xfffffffffffffffc - 0x8) + 0x4, 0x0, 0x0);
>       r4 = [objc_msgSend(@class(NSString), @selector(stringWithCString:encoding:)) retain];
>       free(r6);
>       r0 = loc_2ca9c90(r4, @selector(stringWithCString:encoding:));
>       return r0;
>   }
>   ```
>
>   ```
>   #pragma mark sysctlbyname utils
>   - (NSString *) getSysInfoByName:(char *)typeSpecifier
>   {
>       size_t size;
>       sysctlbyname(typeSpecifier, NULL, &size, NULL, 0);
>       
>       char *answer = malloc(size);
>       sysctlbyname(typeSpecifier, answer, &size, NULL, 0);
>       
>       NSString *results = [NSString stringWithCString:answer encoding: NSUTF8StringEncoding];
>   
>       free(answer);
>       return results;
>   }
>   
>   - (NSString *) platform
>   {
>       return [self getSysInfoByName:"hw.machine"];
>   }
>   
>   ```
>
>   
>
> * cpuCount 
>
>   ```
>    [<UIDevice: 0x16ac14e0> cpuCount]
>    -[UIDevice getSysInfo:3 ]
>    ret:2
>   ```
>
>   
>
> * systemVersion 
>
>   ```
>    [<UIDevice: 0x16ac14e0> systemVersion]
>     [1;36m[knFake] [m[0;36m/Users/devzkn/code/tweak/knwx2018/knFake/knFake/Hook.mm:180[m [0;30;46mDEBUG:[m ProductVersion: 8.1 => (null)
>   
>    ret:8.1
>   ```
>
>   
>
> * _idfv
>
>   ```
>    _idfv = [UIDevice currentDevice].identifierForVendor.UUIDString;
>   ```
>
>   



####  getCurrentCPUType 

> * KNISDUtility.h
>
>   ```
>   typedef NS_ENUM(NSUInteger, CPUType) {
>       CPUTypeARMV6,
>       CPUTypeARMV7,
>       CPUTypeARMV7S,
>       CPUTypeARMV8,
>       CPUTypeARM64,
>       CPUTypeX86,
>       CPUTypeX86_64,
>       CPUTYpeUnsupported
>   };
>   
>   typedef NS_ENUM(NSUInteger, AIProcess) {
>       AIProcessAKD,
>       AIProcessISD,
>       AIProcessUnknown
>   };
>   
>   @interface ISDUtility : NSObject
>   + (AIProcess)processType;
>   + (NSData *)dataFromHexString:(NSString *)string;
>   + (void)printImagesAddress;
>   + (CPUType)getCurrentCPUType;
>   
>   + (NSArray *)propertyNamesOfClass:(Class)cls;
>   + (NSDictionary *)valuesOfInstance:(id)instance;
>   - (NSString *)nameOfInstance:(id)instance inClass:(Class)cls;
>   
>   @end
>   
>   ```
>
>   

```
#import "KNISDUtility.h"

#import <dlfcn.h>
#import <sys/stat.h>
#import <sys/types.h>
#import <sys/sysctl.h>
#import <mach/mach_host.h>
#import <mach/machine.h>
#import <mach-o/dyld.h>
#import <mach-o/loader.h>
#import <mach-o/nlist.h>

#import <objc/runtime.h>
 
@implementation ISDUtility

+ (AIProcess)processType 
{
    NSString *pname = [[NSProcessInfo processInfo] processName];
    
    if ([pname isEqualToString:@"akd"]) {
        return AIProcessAKD;
    } else if ([pname isEqualToString:@"itunesstored"]) {
        return AIProcessISD;
    } else {
        return AIProcessUnknown;
    }
}

+ (NSData *)dataFromHexString:(NSString *)string {
    
    string = [string lowercaseString];
    NSMutableData *data= [NSMutableData new];
    unsigned char whole_byte;
    char byte_chars[3] = {'\0','\0','\0'};
    int i = 0;
    long length = string.length;
    while (i < length-1) {
        char c = [string characterAtIndex:i++];
        if (c < '0' || (c > '9' && c < 'a') || c > 'f')
            continue;
        byte_chars[0] = c;
        byte_chars[1] = [string characterAtIndex:i++];
        whole_byte = strtol(byte_chars, NULL, 16);
        [data appendBytes:&whole_byte length:1];
    }
    return data;
}

+ (void)printImagesAddress {
    
    for (int i = 0; i < _dyld_image_count(); i++) {
        
        char *image_name = (char *)_dyld_get_image_name(i);
        //const struct mach_header *mh = _dyld_get_image_header(i);
        //intptr_t vmaddr_slide = _dyld_get_image_vmaddr_slide(i);
        
        //NSLog(@"[%d]Image name %s at address 0x%llx and ASLR slide 0x%lx.\n", i, image_name, (mach_vm_address_t)mh, vmaddr_slide);
        
        if (strcmp(image_name, "/System/Library/PrivateFrameworks/iTunesStore.framework/Support/itunesstored") == 0)
        {
            //NSLog(@"itunesstored address 0x%llx and ASLR slide 0x%lx.\n", (mach_vm_address_t)mh, vmaddr_slide);
        }
    }
}

+ (CPUType)getCurrentCPUType {
    
    size_t size;
    cpu_type_t type;
    cpu_subtype_t subtype;
    size = sizeof(type);
    sysctlbyname("hw.cputype", &type, &size, NULL, 0);
    
    size = sizeof(subtype);
    sysctlbyname("hw.cpusubtype", &subtype, &size, NULL, 0);
    
    // values for cputype and cpusubtype defined in mach/machine.h
    if (type == CPU_TYPE_X86_64) {
        return CPUTypeX86_64;
    } else if (type == CPU_TYPE_X86) {
        return CPUTypeX86;
    } else if (type == CPU_TYPE_ARM) {
        switch(subtype) {
            case CPU_SUBTYPE_ARM_V6:
                return CPUTypeARMV6;
            case CPU_SUBTYPE_ARM_V7:
                return CPUTypeARMV7;
            case CPU_SUBTYPE_ARM_V7S:
                return CPUTypeARMV7S;
            case CPU_SUBTYPE_ARM_V8:
                return CPUTypeARMV8;
        }
    } else if (type == CPU_TYPE_ARM64) {
        return CPUTypeARM64;
    }
    return CPUTYpeUnsupported;
}

// 一个类的所有属性名字
+ (NSArray<NSString *> *)propertyNamesOfClass:(Class)cls
{
    unsigned count;
    objc_property_t *properties = class_copyPropertyList([cls class], &count);
    NSMutableArray *rv = [NSMutableArray array];
    for (int i = 0; i < count; i++)
    {
        objc_property_t property = properties[i];
        NSString *name = [NSString stringWithUTF8String:property_getName(property)];
        [rv addObject:name];
    }
    free(properties);
    
    return rv;
}

// 一个类所有属性的值
+ (NSDictionary *)valuesOfInstance:(id)instance {
    if (instance == nil)
    {
        NSLog(@"valuesOfInstance error: instance is nil");
        return nil;
    }
    NSArray<NSString *> * properties = [ISDUtility propertyNamesOfClass:[instance class]];
    if (properties == nil)
    {
        NSLog(@"valuesOfInstance error: properties is nil");
        return nil;
    }
    if ([properties count] == 0)
    {
        NSLog(@"valuesOfInstance error: properties is empty");
        return nil;
    }
    NSDictionary *values = [NSMutableDictionary dictionary];
    for (NSString *property in properties) {
        
        if ([property isEqualToString:@"_detailedDescription"]
            || [property isEqualToString:@"debugDescription"]
            || [property isEqualToString:@"description"]
            || [property isEqualToString:@"hash"]) {
            
            continue;
        }
        id value = [instance valueForKey:property];
        //NSString *typeAndValue = [NSString stringWithFormat:@"%@ (%@)" , value, [value class]];
        //[values setValue:typeAndValue forKey:property];
        [values setValue:value forKey:property];
    }
    return values;
}

// 根据实例查找其在类中的名字, 也就是“反射”
- (NSString *)nameOfInstance:(id)instance inClass:(Class)cls {
    unsigned int numIvars = 0;
    NSString *key=nil;
    Ivar * ivars = class_copyIvarList([cls class], &numIvars);
    for(int i = 0; i < numIvars; i++) {
        Ivar thisIvar = ivars[i];
        const char *type = ivar_getTypeEncoding(thisIvar);
        NSString *stringType =  [NSString stringWithCString:type encoding:NSUTF8StringEncoding];
        if (![stringType hasPrefix:@"@"]) {
            continue;
        }
        if ((object_getIvar(cls, thisIvar) == instance)) { //此处若 crash 不要慌！
            key = [NSString stringWithUTF8String:ivar_getName(thisIvar)];
            break;
        }
    }
    free(ivars);
    return key;
}

@end

```



#### sysctl

> * CTL_HW identifiers 
>
>   ```
>   /*
>    * Copyright (c) 2000-2006 Apple Computer, Inc. All rights reserved.
>    *
>    * @APPLE_OSREFERENCE_LICENSE_HEADER_START@
>    *
>    * This file contains Original Code and/or Modifications of Original Code
>    * as defined in and that are subject to the Apple Public Source License
>    * Version 2.0 (the 'License'). You may not use this file except in
>    * compliance with the License. The rights granted to you under the License
>    * may not be used to create, or enable the creation or redistribution of,
>    * unlawful or unlicensed copies of an Apple operating system, or to
>    * circumvent, violate, or enable the circumvention or violation of, any
>    * terms of an Apple operating system software license agreement.
>    *
>    * Please obtain a copy of the License at
>    * http://www.opensource.apple.com/apsl/ and read it before using this file.
>    *
>    * The Original Code and all software distributed under the License are
>    * distributed on an 'AS IS' basis, WITHOUT WARRANTY OF ANY KIND, EITHER
>    * EXPRESS OR IMPLIED, AND APPLE HEREBY DISCLAIMS ALL SUCH WARRANTIES,
>    * INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY,
>    * FITNESS FOR A PARTICULAR PURPOSE, QUIET ENJOYMENT OR NON-INFRINGEMENT.
>    * Please see the License for the specific language governing rights and
>    * limitations under the License.
>    *
>    * @APPLE_OSREFERENCE_LICENSE_HEADER_END@
>    */
>   /* Copyright (c) 1995 NeXT Computer, Inc. All Rights Reserved */
>   /*
>    * Copyright (c) 1989, 1993
>    *	The Regents of the University of California.  All rights reserved.
>    *
>    * This code is derived from software contributed to Berkeley by
>    * Mike Karels at Berkeley Software Design, Inc.
>    *
>    * Redistribution and use in source and binary forms, with or without
>    * modification, are permitted provided that the following conditions
>    * are met:
>    * 1. Redistributions of source code must retain the above copyright
>    *    notice, this list of conditions and the following disclaimer.
>    * 2. Redistributions in binary form must reproduce the above copyright
>    *    notice, this list of conditions and the following disclaimer in the
>    *    documentation and/or other materials provided with the distribution.
>    * 3. All advertising materials mentioning features or use of this software
>    *    must display the following acknowledgement:
>    *	This product includes software developed by the University of
>    *	California, Berkeley and its contributors.
>    * 4. Neither the name of the University nor the names of its contributors
>    *    may be used to endorse or promote products derived from this software
>    *    without specific prior written permission.
>    *
>    * THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS'' AND
>    * ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
>    * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
>    * ARE DISCLAIMED.  IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS BE LIABLE
>    * FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
>    * DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
>    * OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
>    * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
>    * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
>    * OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
>    * SUCH DAMAGE.
>    *
>    *	@(#)sysctl.h	8.1 (Berkeley) 6/2/93
>    */
>   /*
>    * NOTICE: This file was modified by SPARTA, Inc. in 2005 to introduce
>    * support for mandatory and extensible security protections.  This notice
>    * is included in support of clause 2.2 (b) of the Apple Public License,
>    * Version 2.0.
>    */
>   
>   #ifndef _SYS_SYSCTL_H_
>   #define	_SYS_SYSCTL_H_
>   
>   /*
>    * These are for the eproc structure defined below.
>    */
>   #include <sys/cdefs.h>
>   
>   #include <sys/appleapiopts.h>
>   #include <libkern/sysctl.h>
>   
>   #include <sys/proc.h>
>   #include <sys/vm.h>
>   
>   
>   /*
>    * Definitions for sysctl call.  The sysctl call uses a hierarchical name
>    * for objects that can be examined or modified.  The name is expressed as
>    * a sequence of integers.  Like a file path name, the meaning of each
>    * component depends on its place in the hierarchy.  The top-level and kern
>    * identifiers are defined here, and other identifiers are defined in the
>    * respective subsystem header files.
>    */
>   
>   #define CTL_MAXNAME	12	/* largest number of components supported */
>   
>   /*
>    * Each subsystem defined by sysctl defines a list of variables
>    * for that subsystem. Each name is either a node with further
>    * levels defined below it, or it is a leaf of some particular
>    * type given below. Each sysctl level defines a set of name/type
>    * pairs to be used by sysctl(1) in manipulating the subsystem.
>    *
>    * When declaring new sysctl names, use the CTLFLAG_LOCKED flag in the
>    * type to indicate that all necessary locking will be handled
>    * within the sysctl.
>    *
>    * Any sysctl defined without CTLFLAG_LOCKED is considered legacy
>    * and will be protected by a global mutex.
>    *
>    * Note:	This is not optimal, so it is best to handle locking
>    *		yourself, if you are able to do so.  A simple design
>    *		pattern for use to avoid in a single function known
>    *		to potentially be in the paging path ot doing a DMA
>    *		to physical memory in a user space process is:
>    *
>    *			lock
>    *			perform operation vs. local buffer
>    *			unlock
>    *			SYSCTL_OUT(rey, local buffer, length)
>    *
>    *		...this assumes you are not using a deep call graph
>    *		or are unable to pass a local buffer address as a
>    *		parameter into your deep call graph.
>    *
>    *		Note that very large user buffers can fail the wire
>    *		if to do so would require more physical pages than
>    *		are available (the caller will get an ENOMEM error,
>    *		see sysctl_mem_hold() for details).
>    */
>   struct ctlname {
>   	char	*ctl_name;	/* subsystem name */
>   	int	ctl_type;	/* type of name */
>   };
>   
>   #define CTLTYPE		0xf	/* Mask for the type */
>   #define	CTLTYPE_NODE	1	/* name is a node */
>   #define	CTLTYPE_INT	2	/* name describes an integer */
>   #define	CTLTYPE_STRING	3	/* name describes a string */
>   #define	CTLTYPE_QUAD	4	/* name describes a 64-bit number */
>   #define	CTLTYPE_OPAQUE	5	/* name describes a structure */
>   #define	CTLTYPE_STRUCT	CTLTYPE_OPAQUE	/* name describes a structure */
>   
>   #define CTLFLAG_RD	0x80000000	/* Allow reads of variable */
>   #define CTLFLAG_WR	0x40000000	/* Allow writes to the variable */
>   #define CTLFLAG_RW	(CTLFLAG_RD|CTLFLAG_WR)
>   #define CTLFLAG_NOLOCK	0x20000000	/* XXX Don't Lock */
>   #define CTLFLAG_ANYBODY	0x10000000	/* All users can set this var */
>   #define CTLFLAG_SECURE	0x08000000	/* Permit set only if securelevel<=0 */
>   #define CTLFLAG_MASKED	0x04000000	/* deprecated variable, do not display */
>   #define CTLFLAG_NOAUTO	0x02000000	/* do not auto-register */
>   #define CTLFLAG_KERN	0x01000000	/* valid inside the kernel */
>   #define CTLFLAG_LOCKED	0x00800000	/* node will handle locking itself */
>   #define CTLFLAG_OID2	0x00400000	/* struct sysctl_oid has version info */
>   
>   /*
>    * USE THIS instead of a hardwired number from the categories below
>    * to get dynamically assigned sysctl entries using the linker-set
>    * technology. This is the way nearly all new sysctl variables should
>    * be implemented.
>    *
>    * e.g. SYSCTL_INT(_parent, OID_AUTO, name, CTLFLAG_RW, &variable, 0, "");
>    *
>    * Note that linker set technology will automatically register all nodes
>    * declared like this on kernel initialization, UNLESS they are defined
>    * in I/O-Kit. In this case, you have to call sysctl_register_oid()
>    * manually - just like in a KEXT.
>    */
>   #define OID_AUTO	(-1)
>   #define OID_AUTO_START 100 /* conventional */
>   
>   #define SYSCTL_HANDLER_ARGS (struct sysctl_oid *oidp, void *arg1, int arg2, \
>   	struct sysctl_req *req)
>   
>   
>   /*
>    * This describes the access space for a sysctl request.  This is needed
>    * so that we can use the interface from the kernel or from user-space.
>    */
>   struct sysctl_req {
>   	struct proc	*p;
>   	int		lock;
>   	user_addr_t	oldptr;		/* pointer to user supplied buffer */
>   	size_t		oldlen;		/* user buffer length (also returned) */
>   	size_t		oldidx;		/* total data iteratively copied out */
>   	int		(*oldfunc)(struct sysctl_req *, const void *, size_t);
>   	user_addr_t	newptr;		/* buffer containing new value */
>   	size_t		newlen;		/* length of new value */
>   	size_t		newidx;		/* total data iteratively copied in */
>   	int		(*newfunc)(struct sysctl_req *, void *, size_t);
>   };
>   
>   SLIST_HEAD(sysctl_oid_list, sysctl_oid);
>   
>   #define SYSCTL_OID_VERSION	1	/* current OID structure version */
>   
>   /*
>    * This describes one "oid" in the MIB tree.  Potentially more nodes can
>    * be hidden behind it, expanded by the handler.
>    *
>    * NOTES:	We implement binary comparibility between CTLFLAG_OID2 and
>    *		pre-CTLFLAG_OID2 structure in sysctl_register_oid() and in
>    *		sysctl_unregister_oid() using the fact that the fields up
>    *		to oid_fmt are unchanged, and that the field immediately
>    *		following is on an alignment boundary following a pointer
>    *		type and is also a pointer.  This lets us get the previous
>    *		size of the structure, and the copy-cut-off point, using
>    *		the offsetof() language primitive, and these values  are
>    *		used in conjunction with the fact that earlier and future
>    *		statically compiled sysctl_oid structures are declared via
>    *		macros.  This lets us overload the macros so that the addition
>    *		of the CTLFLAG_OID2 in newly compiled code containing sysctl
>    *		node declarations, subsequently allowing us to to avoid
>    *		changing the KPI used for non-static (un)registration in
>    *		KEXTs.
>    *
>    *		This depends on the fact that people declare SYSCTLs,
>    *		rather than declaring sysctl_oid structures.  All new code
>    *		should avoid declaring struct sysctl_oid's directly without
>    *		the macros; the current risk for this is limited to losing
>    *		your description field and ending up with a malloc'ed copy,
>    *		as if it were a legacy binary static declaration via SYSCTL;
>    *		in the future, we may deprecate access to a named structure
>    *		type in third party code.  Use the macros, or our code will
>    *		end up with compile errors when that happens.
>    *
>    *		Please try to include a long description of the field in any
>    *		new sysctl declarations (all the macros support this).  This
>    *		field may be the only human readable documentation your users
>    *		get for your sysctl.
>    */
>   struct sysctl_oid {
>   	struct sysctl_oid_list *oid_parent;
>   	SLIST_ENTRY(sysctl_oid) oid_link;
>   	int		oid_number;
>   	int		oid_kind;
>   	void		*oid_arg1;
>   	int		oid_arg2;
>   	const char	*oid_name;
>   	int 		(*oid_handler) SYSCTL_HANDLER_ARGS;
>   	const char	*oid_fmt;
>   	const char	*oid_descr; /* offsetof() field / long description */
>   	int		oid_version;
>   	int		oid_refcnt;
>   };
>   
>   #define SYSCTL_IN(r, p, l) (r->newfunc)(r, p, l)
>   #define SYSCTL_OUT(r, p, l) (r->oldfunc)(r, p, l)
>   
>   typedef int (* sysctl_handler_t) SYSCTL_HANDLER_ARGS;
>   
>   __BEGIN_DECLS
>   
>   /* old interface */
>   int sysctl_handle_int SYSCTL_HANDLER_ARGS;
>   int sysctl_handle_long SYSCTL_HANDLER_ARGS;
>   int sysctl_handle_quad SYSCTL_HANDLER_ARGS;
>   int sysctl_handle_int2quad SYSCTL_HANDLER_ARGS;
>   int sysctl_handle_string SYSCTL_HANDLER_ARGS;
>   int sysctl_handle_opaque SYSCTL_HANDLER_ARGS;
>   /* new interface */
>   int sysctl_io_number(struct sysctl_req *req, long long bigValue, size_t valueSize, void *pValue, int *changed);
>   int sysctl_io_string(struct sysctl_req *req, char *pValue, size_t valueSize, int trunc, int *changed);
>   int sysctl_io_opaque(struct sysctl_req *req, void *pValue, size_t valueSize, int *changed);
>   
>   /*
>    * These functions are used to add/remove an oid from the mib.
>    */
>   void sysctl_register_oid(struct sysctl_oid *oidp);
>   void sysctl_unregister_oid(struct sysctl_oid *oidp);
>   
>   /* Deprecated */
>   void sysctl_register_fixed(void) __deprecated;
>   
>   __END_DECLS
>   
>   /* Declare an oid to allow child oids to be added to it. */
>   #define SYSCTL_DECL(name)					\
>   	extern struct sysctl_oid_list sysctl_##name##_children
>   
>   #define SYSCTL_LINKER_SET_ENTRY(a, b)
>   /*
>    * Macros to define sysctl entries.  Which to use?  Pure data that are
>    * returned without modification, SYSCTL_<data type> is for you, like
>    * SYSCTL_QUAD for a 64-bit value.  When you want to run a handler of your
>    * own, SYSCTL_PROC.
>    *
>    * parent:	parent in name hierarchy (e.g. _kern for "kern")
>    * nbr:		ID.  Almost certainly OID_AUTO ("pick one for me") for you.
>    * name:	name for this particular item (e.g. "thesysctl" for "kern.thesysctl")
>    * kind/access: Control flags (CTLFLAG_*).  Some notable options include:
>    * 			CTLFLAG_ANYBODY: 	non-root users allowed
>    * 			CTLFLAG_MASKED:	 	don't show in sysctl listing in userland
>    * 			CTLFLAG_LOCKED:		does own locking (no additional protection needed)
>    * 			CTLFLAG_KERN:		valid inside kernel (best avoided generally)
>    * 			CTLFLAG_WR:		"new" value accepted
>    * a1, a2:	entry-data, passed to handler (see specific macros)
>    * Format String: Tells "sysctl" tool how to print data from this entry.
>    *	 		"A" - string
>    *	 		"I" - list of integers. "IU" - list of unsigned integers. space-separated.
>    *	 		"-" - do not print
>    *	 		"L" - longs, as ints with I
>    *			"P" - pointer
>    * 			"Q" - quads
>    * 			"S","T" - clock info, see sysctl.c in system_cmds (you probably don't need this)
>    * Description: unused
>    */
>   
>   
>   /* This constructs a "raw" MIB oid. */
>   #define SYSCTL_STRUCT_INIT(parent, nbr, name, kind, a1, a2, handler, fmt, descr) \
>   	{												\
>   		&sysctl_##parent##_children, { 0 },			\
>   		nbr, (int)(kind|CTLFLAG_OID2), a1, (int)(a2), #name, handler, fmt, descr, SYSCTL_OID_VERSION, 0 \
>   	}
>   
>   #define SYSCTL_OID(parent, nbr, name, kind, a1, a2, handler, fmt, descr) \
>   	struct sysctl_oid sysctl_##parent##_##name = SYSCTL_STRUCT_INIT(parent, nbr, name, kind, a1, a2, handler, fmt, descr); \
>   	SYSCTL_LINKER_SET_ENTRY(__sysctl_set, sysctl_##parent##_##name)
>   
>   /* This constructs a node from which other oids can hang. */
>   #define SYSCTL_NODE(parent, nbr, name, access, handler, descr)		    \
>   	struct sysctl_oid_list sysctl_##parent##_##name##_children;	    \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_NODE|access,		    \
>   		   (void*)&sysctl_##parent##_##name##_children, 0, handler, \
>   		   "N", descr);
>   
>   /* Oid for a string.  len can be 0 to indicate '\0' termination. */
>   #define SYSCTL_STRING(parent, nbr, name, access, arg, len, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_STRING|access, \
>   		arg, len, sysctl_handle_string, "A", descr)
>   
>   #define SYSCTL_COMPAT_INT(parent, nbr, name, access, ptr, val, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>                  ptr, val, sysctl_handle_int, "I", descr)
>   
>   #define SYSCTL_COMPAT_UINT(parent, nbr, name, access, ptr, val, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>   		ptr, val, sysctl_handle_int, "IU", descr)
>   
>   /* Oid for an int.  If ptr is NULL, val is returned. */
>   #define SYSCTL_INT(parent, nbr, name, access, ptr, val, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>                  ptr, val, sysctl_handle_int, "I", descr); \
>   	typedef char _sysctl_##parent##_##name##_size_check[(__builtin_constant_p(ptr) || sizeof(*(ptr)) == sizeof(int)) ? 0 : -1];
>   
>   /* Oid for an unsigned int.  If ptr is NULL, val is returned. */
>   #define SYSCTL_UINT(parent, nbr, name, access, ptr, val, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>   		ptr, val, sysctl_handle_int, "IU", descr); \
>   	typedef char _sysctl_##parent##_##name##_size_check[(__builtin_constant_p(ptr) || sizeof(*(ptr)) == sizeof(unsigned int)) ? 0 : -1];
>   
>   /* Oid for a long.  The pointer must be non NULL. */
>   #define SYSCTL_LONG(parent, nbr, name, access, ptr, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>   		ptr, 0, sysctl_handle_long, "L", descr); \
>   	typedef char _sysctl_##parent##_##name##_size_check[(__builtin_constant_p(ptr) || sizeof(*(ptr)) == sizeof(long)) ? 0 : -1];
>   
>   /* Oid for a unsigned long.  The pointer must be non NULL. */
>   #define SYSCTL_ULONG(parent, nbr, name, access, ptr, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_INT|access, \
>   		ptr, 0, sysctl_handle_long, "LU", descr); \
>   	typedef char _sysctl_##parent##_##name##_size_check[(__builtin_constant_p(ptr) || sizeof(*(ptr)) == sizeof(unsigned long)) ? 0 : -1];
>   
>   /* Oid for a quad.  The pointer must be non NULL. */
>   #define SYSCTL_QUAD(parent, nbr, name, access, ptr, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_QUAD|access, \
>   		ptr, 0, sysctl_handle_quad, "Q", descr); \
>   	typedef char _sysctl_##parent##_##name##_size_check[(__builtin_constant_p(ptr) || sizeof(*(ptr)) == sizeof(long long)) ? 0 : -1];
>   
>   /* Oid for an opaque object.  Specified by a pointer and a length. */
>   #define SYSCTL_OPAQUE(parent, nbr, name, access, ptr, len, fmt, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_OPAQUE|access, \
>   		ptr, len, sysctl_handle_opaque, fmt, descr)
>   
>   /* Oid for a struct.  Specified by a pointer and a type. */
>   #define SYSCTL_STRUCT(parent, nbr, name, access, ptr, type, descr) \
>   	SYSCTL_OID(parent, nbr, name, CTLTYPE_OPAQUE|access, \
>   		ptr, sizeof(struct type), sysctl_handle_opaque, \
>   		"S," #type, descr)
>   
>   /*
>    * Oid for a procedure.  Specified by a pointer and an arg.
>    * CTLTYPE_* macros can determine how the "sysctl" tool deals with
>    * input (e.g. converting to int).
>    */
>   #define SYSCTL_PROC(parent, nbr, name, access, ptr, arg, handler, fmt, descr) \
>   	SYSCTL_OID(parent, nbr, name, access, \
>   		ptr, arg, handler, fmt, descr)
>   
>   
>   extern struct sysctl_oid_list sysctl__children;
>   SYSCTL_DECL(_kern);
>   SYSCTL_DECL(_sysctl);
>   SYSCTL_DECL(_vm);
>   SYSCTL_DECL(_vfs);
>   SYSCTL_DECL(_net);
>   SYSCTL_DECL(_debug);
>   SYSCTL_DECL(_hw);
>   SYSCTL_DECL(_machdep);
>   SYSCTL_DECL(_user);
>   
>   
>   
>   #ifndef SYSCTL_SKMEM_UPDATE_FIELD
>   
>   #define	SYSCTL_SKMEM 0
>   #define	SYSCTL_SKMEM_UPDATE_FIELD(field, value)
>   #define	SYSCTL_SKMEM_UPDATE_AT_OFFSET(offset, value)
>   #define	SYSCTL_SKMEM_INT(parent, oid, sysctl_name, access, ptr, offset, descr) \
>   	SYSCTL_INT(parent, oid, sysctl_name, access, ptr, 0, descr)
>   
>   #define	SYSCTL_SKMEM_TCP_INT(oid, sysctl_name, access, variable_type,	\
>   							 variable_name, initial_value, descr)		\
>   	variable_type variable_name = initial_value;						\
>   	SYSCTL_SKMEM_INT(_net_inet_tcp, oid, sysctl_name, access,			\
>   					 &variable_name, 0, descr)
>   
>   #else /* SYSCTL_SKMEM_UPDATE_FIELD */
>   #define	SYSCTL_SKMEM 1
>   #endif /* SYSCTL_SKMEM_UPDATE_FIELD */
>   
>   
>   
>   
>   
>   #ifdef SYSCTL_DEF_ENABLED
>   /*
>    * Top-level identifiers
>    */
>   #define	CTL_UNSPEC	0		/* unused */
>   #define	CTL_KERN	1		/* "high kernel": proc, limits */
>   #define	CTL_VM		2		/* virtual memory */
>   #define	CTL_VFS		3		/* file system, mount type is next */
>   #define	CTL_NET		4		/* network, see socket.h */
>   #define	CTL_DEBUG	5		/* debugging parameters */
>   #define	CTL_HW		6		/* generic cpu/io */
>   #define	CTL_MACHDEP	7		/* machine dependent */
>   #define	CTL_USER	8		/* user-level */
>   #define	CTL_MAXID	9		/* number of valid top-level ids */
>   
>   #define CTL_NAMES { \
>   	{ 0, 0 }, \
>   	{ "kern", CTLTYPE_NODE }, \
>   	{ "vm", CTLTYPE_NODE }, \
>   	{ "vfs", CTLTYPE_NODE }, \
>   	{ "net", CTLTYPE_NODE }, \
>   	{ "debug", CTLTYPE_NODE }, \
>   	{ "hw", CTLTYPE_NODE }, \
>   	{ "machdep", CTLTYPE_NODE }, \
>   	{ "user", CTLTYPE_NODE }, \
>   }
>   
>   /*
>    * CTL_KERN identifiers
>    */
>   #define	KERN_OSTYPE	 	 1	/* string: system version */
>   #define	KERN_OSRELEASE	 	 2	/* string: system release */
>   #define	KERN_OSREV	 	 3	/* int: system revision */
>   #define	KERN_VERSION	 	 4	/* string: compile time info */
>   #define	KERN_MAXVNODES	 	 5	/* int: max vnodes */
>   #define	KERN_MAXPROC	 	 6	/* int: max processes */
>   #define	KERN_MAXFILES	 	 7	/* int: max open files */
>   #define	KERN_ARGMAX	 	 8	/* int: max arguments to exec */
>   #define	KERN_SECURELVL	 	 9	/* int: system security level */
>   #define	KERN_HOSTNAME		10	/* string: hostname */
>   #define	KERN_HOSTID		11	/* int: host identifier */
>   #define	KERN_CLOCKRATE		12	/* struct: struct clockrate */
>   #define	KERN_VNODE		13	/* struct: vnode structures */
>   #define	KERN_PROC		14	/* struct: process entries */
>   #define	KERN_FILE		15	/* struct: file entries */
>   #define	KERN_PROF		16	/* node: kernel profiling info */
>   #define	KERN_POSIX1		17	/* int: POSIX.1 version */
>   #define	KERN_NGROUPS		18	/* int: # of supplemental group ids */
>   #define	KERN_JOB_CONTROL	19	/* int: is job control available */
>   #define	KERN_SAVED_IDS		20	/* int: saved set-user/group-ID */
>   #define	KERN_BOOTTIME		21	/* struct: time kernel was booted */
>   #define KERN_NISDOMAINNAME	22	/* string: YP domain name */
>   #define KERN_DOMAINNAME		KERN_NISDOMAINNAME
>   #define	KERN_MAXPARTITIONS	23	/* int: number of partitions/disk */
>   #define	KERN_KDEBUG			24	/* int: kernel trace points */
>   #define KERN_UPDATEINTERVAL	25	/* int: update process sleep time */
>   #define KERN_OSRELDATE		26	/* int: OS release date */
>   #define KERN_NTP_PLL		27	/* node: NTP PLL control */
>   #define	KERN_BOOTFILE		28	/* string: name of booted kernel */
>   #define	KERN_MAXFILESPERPROC	29	/* int: max open files per proc */
>   #define	KERN_MAXPROCPERUID 	30	/* int: max processes per uid */
>   #define KERN_DUMPDEV		31	/* dev_t: device to dump on */
>   #define	KERN_IPC		32	/* node: anything related to IPC */
>   #define	KERN_DUMMY		33	/* unused */
>   #define	KERN_PS_STRINGS	34	/* int: address of PS_STRINGS */
>   #define	KERN_USRSTACK32	35	/* int: address of USRSTACK */
>   #define	KERN_LOGSIGEXIT	36	/* int: do we log sigexit procs? */
>   #define KERN_SYMFILE		37	/* string: kernel symbol filename */
>   #define KERN_PROCARGS		38
>                                /* 39 was KERN_PCSAMPLES... now deprecated */
>   #define KERN_NETBOOT		40	/* int: are we netbooted? 1=yes,0=no */
>                                /* 41 was KERN_PANICINFO : panic UI information (deprecated) */
>   #define	KERN_SYSV		42	/* node: System V IPC information */
>   #define KERN_AFFINITY		43	/* xxx */
>   #define KERN_TRANSLATE	   	44	/* xxx */
>   #define KERN_CLASSIC	   	KERN_TRANSLATE	/* XXX backwards compat */
>   #define KERN_EXEC		45	/* xxx */
>   #define KERN_CLASSICHANDLER	KERN_EXEC /* XXX backwards compatibility */
>   #define	KERN_AIOMAX		46	/* int: max aio requests */
>   #define	KERN_AIOPROCMAX		47	/* int: max aio requests per process */
>   #define	KERN_AIOTHREADS		48	/* int: max aio worker threads */
>   #ifdef __APPLE_API_UNSTABLE
>   #define	KERN_PROCARGS2		49
>   #endif /* __APPLE_API_UNSTABLE */
>   #define KERN_COREFILE		50	/* string: corefile format string */
>   #define KERN_COREDUMP		51	/* int: whether to coredump at all */
>   #define	KERN_SUGID_COREDUMP	52	/* int: whether to dump SUGID cores */
>   #define	KERN_PROCDELAYTERM	53	/* int: set/reset current proc for delayed termination during shutdown */
>   #define KERN_SHREG_PRIVATIZABLE	54	/* int: can shared regions be privatized ? */
>                                /* 55 was KERN_PROC_LOW_PRI_IO... now deprecated */
>   #define	KERN_LOW_PRI_WINDOW	56	/* int: set/reset throttle window - milliseconds */
>   #define	KERN_LOW_PRI_DELAY	57	/* int: set/reset throttle delay - milliseconds */
>   #define	KERN_POSIX		58	/* node: posix tunables */
>   #define	KERN_USRSTACK64		59	/* LP64 user stack query */
>   #define KERN_NX_PROTECTION	60	/* int: whether no-execute protection is enabled */
>   #define	KERN_TFP 		61	/* Task for pid settings */
>   #define	KERN_PROCNAME 		62	/* setup process program  name(2*MAXCOMLEN) */
>   #define	KERN_THALTSTACK		63	/* for compat with older x86 and does nothing */
>   #define	KERN_SPECULATIVE_READS	64	/* int: whether speculative reads are disabled */
>   #define	KERN_OSVERSION		65	/* for build number i.e. 9A127 */
>   #define	KERN_SAFEBOOT		66	/* are we booted safe? */
>   			/*	67 was KERN_LCTX (login context) */
>   #define KERN_RAGEVNODE		68
>   #define KERN_TTY		69	/* node: tty settings */
>   #define KERN_CHECKOPENEVT       70      /* spi: check the VOPENEVT flag on vnodes at open time */
>   #define	KERN_THREADNAME		71	/* set/get thread name */
>   #define	KERN_MAXID		72	/* number of valid kern ids */
>   /*
>    * Don't add any more sysctls like this.  Instead, use the SYSCTL_*() macros
>    * and OID_AUTO. This will have the added benefit of not having to recompile
>    * sysctl(8) to pick up your changes.
>    */
>   
>   #if COUNT_SYSCALLS && defined(KERNEL)
>   #define	KERN_COUNT_SYSCALLS (KERN_OSTYPE + 1000)	/* keep called count for each bsd syscall */
>   #endif
>   
>   #if defined(__LP64__)
>   #define	KERN_USRSTACK KERN_USRSTACK64
>   #else
>   #define	KERN_USRSTACK KERN_USRSTACK32
>   #endif
>   
>   
>   /* KERN_RAGEVNODE types */
>   #define KERN_RAGE_PROC		1
>   #define KERN_RAGE_THREAD	2
>   #define KERN_UNRAGE_PROC	3
>   #define KERN_UNRAGE_THREAD	4
>   
>   /* KERN_OPENEVT types */
>   #define KERN_OPENEVT_PROC     1
>   #define KERN_UNOPENEVT_PROC   2
>   
>   /* KERN_TFP types */
>   #define KERN_TFP_POLICY 		1
>   
>   /* KERN_TFP_POLICY values . All policies allow task port for self */
>   #define KERN_TFP_POLICY_DENY 		0 	/* Deny Mode: None allowed except privileged */
>   #define KERN_TFP_POLICY_DEFAULT 	2	/* Default  Mode: related ones allowed and upcall authentication */
>   
>   /* KERN_KDEBUG types */
>   #define KERN_KDEFLAGS         1
>   #define KERN_KDDFLAGS         2
>   #define KERN_KDENABLE         3
>   #define KERN_KDSETBUF         4
>   #define KERN_KDGETBUF         5
>   #define KERN_KDSETUP          6
>   #define KERN_KDREMOVE         7
>   #define KERN_KDSETREG         8
>   #define KERN_KDGETREG         9
>   #define KERN_KDREADTR         10
>   #define KERN_KDPIDTR          11
>   #define KERN_KDTHRMAP         12
>   /* Don't use 13 as it is overloaded with KERN_VNODE */
>   #define KERN_KDPIDEX          14
>   #define KERN_KDSETRTCDEC      15 /* obsolete */
>   #define KERN_KDGETENTROPY     16 /* obsolete */
>   #define KERN_KDWRITETR        17
>   #define KERN_KDWRITEMAP       18
>   #define KERN_KDTEST           19
>   /* 20 unused */
>   #define KERN_KDREADCURTHRMAP  21
>   #define KERN_KDSET_TYPEFILTER 22
>   #define KERN_KDBUFWAIT        23
>   #define KERN_KDCPUMAP         24
>   /* 25 - 26 unused */
>   #define KERN_KDWRITEMAP_V3    27
>   #define KERN_KDWRITETR_V3     28
>   
>   #define CTL_KERN_NAMES { \
>   	{ 0, 0 }, \
>   	{ "ostype", CTLTYPE_STRING }, \
>   	{ "osrelease", CTLTYPE_STRING }, \
>   	{ "osrevision", CTLTYPE_INT }, \
>   	{ "version", CTLTYPE_STRING }, \
>   	{ "maxvnodes", CTLTYPE_INT }, \
>   	{ "maxproc", CTLTYPE_INT }, \
>   	{ "maxfiles", CTLTYPE_INT }, \
>   	{ "argmax", CTLTYPE_INT }, \
>   	{ "securelevel", CTLTYPE_INT }, \
>   	{ "hostname", CTLTYPE_STRING }, \
>   	{ "hostid", CTLTYPE_INT }, \
>   	{ "clockrate", CTLTYPE_STRUCT }, \
>   	{ "vnode", CTLTYPE_STRUCT }, \
>   	{ "proc", CTLTYPE_STRUCT }, \
>   	{ "file", CTLTYPE_STRUCT }, \
>   	{ "profiling", CTLTYPE_NODE }, \
>   	{ "posix1version", CTLTYPE_INT }, \
>   	{ "ngroups", CTLTYPE_INT }, \
>   	{ "job_control", CTLTYPE_INT }, \
>   	{ "saved_ids", CTLTYPE_INT }, \
>   	{ "boottime", CTLTYPE_STRUCT }, \
>   	{ "nisdomainname", CTLTYPE_STRING }, \
>   	{ "maxpartitions", CTLTYPE_INT }, \
>   	{ "kdebug", CTLTYPE_INT }, \
>   	{ "update", CTLTYPE_INT }, \
>   	{ "osreldate", CTLTYPE_INT }, \
>   	{ "ntp_pll", CTLTYPE_NODE }, \
>   	{ "bootfile", CTLTYPE_STRING }, \
>   	{ "maxfilesperproc", CTLTYPE_INT }, \
>   	{ "maxprocperuid", CTLTYPE_INT }, \
>   	{ "dumpdev", CTLTYPE_STRUCT }, /* we lie; don't print as int */ \
>   	{ "ipc", CTLTYPE_NODE }, \
>   	{ "dummy", CTLTYPE_INT }, \
>   	{ "dummy", CTLTYPE_INT }, \
>   	{ "usrstack", CTLTYPE_INT }, \
>   	{ "logsigexit", CTLTYPE_INT }, \
>   	{ "symfile",CTLTYPE_STRING },\
>   	{ "procargs",CTLTYPE_STRUCT },\
>           { "dummy", CTLTYPE_INT },		/* deprecated pcsamples */ \
>   	{ "netboot", CTLTYPE_INT }, \
>   	{ "dummy", CTLTYPE_INT }, 		/* deprecated: panicinfo */ \
>   	{ "sysv", CTLTYPE_NODE }, \
>   	{ "dummy", CTLTYPE_INT }, \
>   	{ "dummy", CTLTYPE_INT }, \
>   	{ "exec", CTLTYPE_NODE }, \
>   	{ "aiomax", CTLTYPE_INT }, \
>   	{ "aioprocmax", CTLTYPE_INT }, \
>   	{ "aiothreads", CTLTYPE_INT }, \
>   	{ "procargs2",CTLTYPE_STRUCT }, \
>   	{ "corefile",CTLTYPE_STRING }, \
>   	{ "coredump", CTLTYPE_INT }, \
>   	{ "sugid_coredump", CTLTYPE_INT }, \
>   	{ "delayterm", CTLTYPE_INT }, \
>   	{ "shreg_private", CTLTYPE_INT }, \
>   	{ "proc_low_pri_io", CTLTYPE_INT }, \
>   	{ "low_pri_window", CTLTYPE_INT }, \
>   	{ "low_pri_delay", CTLTYPE_INT }, \
>   	{ "posix", CTLTYPE_NODE }, \
>   	{ "usrstack64", CTLTYPE_QUAD }, \
>   	{ "nx", CTLTYPE_INT }, \
>   	{ "tfp", CTLTYPE_NODE }, \
>   	{ "procname", CTLTYPE_STRING }, \
>   	{ "threadsigaltstack", CTLTYPE_INT }, \
>   	{ "speculative_reads_disabled", CTLTYPE_INT }, \
>   	{ "osversion", CTLTYPE_STRING }, \
>   	{ "safeboot", CTLTYPE_INT }, \
>   	{ "dummy", CTLTYPE_INT }, 		/* deprecated: lctx */ \
>   	{ "rage_vnode", CTLTYPE_INT }, \
>   	{ "tty", CTLTYPE_NODE },	\
>   	{ "check_openevt", CTLTYPE_INT }, \
>   	{ "thread_name", CTLTYPE_STRING } \
>   }
>   
>   /*
>    * CTL_VFS identifiers
>    */
>   #define CTL_VFS_NAMES { \
>   	{ "vfsconf", CTLTYPE_STRUCT } \
>   }
>   
>   /*
>    * KERN_PROC subtypes
>    */
>   #define KERN_PROC_ALL		0	/* everything */
>   #define	KERN_PROC_PID		1	/* by process id */
>   #define	KERN_PROC_PGRP		2	/* by process group id */
>   #define	KERN_PROC_SESSION	3	/* by session of pid */
>   #define	KERN_PROC_TTY		4	/* by controlling tty */
>   #define	KERN_PROC_UID		5	/* by effective uid */
>   #define	KERN_PROC_RUID		6	/* by real uid */
>   #define	KERN_PROC_LCID		7	/* by login context id */
>   
>   
>   
>   /*
>    * KERN_IPC identifiers
>    */
>   #define KIPC_MAXSOCKBUF		1	/* int: max size of a socket buffer */
>   #define	KIPC_SOCKBUF_WASTE	2	/* int: wastage factor in sockbuf */
>   #define	KIPC_SOMAXCONN		3	/* int: max length of connection q */
>   #define	KIPC_MAX_LINKHDR	4	/* int: max length of link header */
>   #define	KIPC_MAX_PROTOHDR	5	/* int: max length of network header */
>   #define	KIPC_MAX_HDR		6	/* int: max total length of headers */
>   #define	KIPC_MAX_DATALEN	7	/* int: max length of data? */
>   #define	KIPC_MBSTAT		8	/* struct: mbuf usage statistics */
>   #define	KIPC_NMBCLUSTERS	9	/* int: maximum mbuf clusters */
>   #define KIPC_SOQLIMITCOMPAT	10	/* int: socket queue limit */
>   
>   /*
>    * CTL_VM identifiers
>    */
>   #define	VM_METER	1		/* struct vmmeter */
>   #define	VM_LOADAVG	2		/* struct loadavg */
>   /*
>    * Note: "3" was skipped sometime ago and should probably remain unused
>    * to avoid any new entry from being accepted by older kernels...
>    */
>   #define	VM_MACHFACTOR	4		/* struct loadavg with mach factor*/
>   #define VM_SWAPUSAGE	5		/* total swap usage */
>   #define	VM_MAXID	6		/* number of valid vm ids */
>   
>   #define	CTL_VM_NAMES { \
>   	{ 0, 0 }, \
>   	{ "vmmeter", CTLTYPE_STRUCT }, \
>   	{ "loadavg", CTLTYPE_STRUCT }, \
>   	{ 0, 0 }, /* placeholder for "3" (see comment above) */ \
>   	{ "dummy", CTLTYPE_INT }, \
>   	{ "swapusage", CTLTYPE_STRUCT } \
>   }
>   
>   struct xsw_usage {
>   	u_int64_t	xsu_total;
>   	u_int64_t	xsu_avail;
>   	u_int64_t	xsu_used;
>   	u_int32_t	xsu_pagesize;
>   	boolean_t	xsu_encrypted;
>   };
>   
>   #ifdef __APPLE_API_PRIVATE
>   /* Load average structure.  Use of fixpt_t assume <sys/types.h> in scope. */
>   /* XXX perhaps we should protect fixpt_t, and define it here (or discard it) */
>   struct loadavg {
>   	fixpt_t	ldavg[3];
>   	long	fscale;
>   };
>   extern struct loadavg averunnable;
>   #define LSCALE	1000		/* scaling for "fixed point" arithmetic */
>   
>   #endif /* __APPLE_API_PRIVATE */
>   
>   
>   /*
>    * CTL_HW identifiers
>    */
>   #define	HW_MACHINE	 1		/* string: machine class */
>   #define	HW_MODEL	 2		/* string: specific machine model */
>   #define	HW_NCPU		 3		/* int: number of cpus */
>   #define	HW_BYTEORDER	 4		/* int: machine byte order */
>   #define	HW_PHYSMEM	 5		/* int: total memory */
>   #define	HW_USERMEM	 6		/* int: non-kernel memory */
>   #define	HW_PAGESIZE	 7		/* int: software page size */
>   #define	HW_DISKNAMES	 8		/* strings: disk drive names */
>   #define	HW_DISKSTATS	 9		/* struct: diskstats[] */
>   #define	HW_EPOCH  	10		/* int: 0 for Legacy, else NewWorld */
>   #define HW_FLOATINGPT	11		/* int: has HW floating point? */
>   #define HW_MACHINE_ARCH	12		/* string: machine architecture */
>   #define HW_VECTORUNIT	13		/* int: has HW vector unit? */
>   #define HW_BUS_FREQ	14		/* int: Bus Frequency */
>   #define HW_CPU_FREQ	15		/* int: CPU Frequency */
>   #define HW_CACHELINE	16		/* int: Cache Line Size in Bytes */
>   #define HW_L1ICACHESIZE	17		/* int: L1 I Cache Size in Bytes */
>   #define HW_L1DCACHESIZE	18		/* int: L1 D Cache Size in Bytes */
>   #define HW_L2SETTINGS	19		/* int: L2 Cache Settings */
>   #define HW_L2CACHESIZE	20		/* int: L2 Cache Size in Bytes */
>   #define HW_L3SETTINGS	21		/* int: L3 Cache Settings */
>   #define HW_L3CACHESIZE	22		/* int: L3 Cache Size in Bytes */
>   #define HW_TB_FREQ	23		/* int: Bus Frequency */
>   #define HW_MEMSIZE	24		/* uint64_t: physical ram size */
>   #define HW_AVAILCPU	25		/* int: number of available CPUs */
>   #define	HW_MAXID	26		/* number of valid hw ids */
>   
>   #define CTL_HW_NAMES { \
>   	{ 0, 0 }, \
>   	{ "machine", CTLTYPE_STRING }, \
>   	{ "model", CTLTYPE_STRING }, \
>   	{ "ncpu", CTLTYPE_INT }, \
>   	{ "byteorder", CTLTYPE_INT }, \
>   	{ "physmem", CTLTYPE_INT }, \
>   	{ "usermem", CTLTYPE_INT }, \
>   	{ "pagesize", CTLTYPE_INT }, \
>   	{ "disknames", CTLTYPE_STRUCT }, \
>   	{ "diskstats", CTLTYPE_STRUCT }, \
>   	{ "epoch", CTLTYPE_INT }, \
>   	{ "floatingpoint", CTLTYPE_INT }, \
>   	{ "machinearch", CTLTYPE_STRING }, \
>   	{ "vectorunit", CTLTYPE_INT }, \
>   	{ "busfrequency", CTLTYPE_INT }, \
>   	{ "cpufrequency", CTLTYPE_INT }, \
>   	{ "cachelinesize", CTLTYPE_INT }, \
>   	{ "l1icachesize", CTLTYPE_INT }, \
>   	{ "l1dcachesize", CTLTYPE_INT }, \
>   	{ "l2settings", CTLTYPE_INT }, \
>   	{ "l2cachesize", CTLTYPE_INT }, \
>   	{ "l3settings", CTLTYPE_INT }, \
>   	{ "l3cachesize", CTLTYPE_INT }, \
>   	{ "tbfrequency", CTLTYPE_INT }, \
>   	{ "memsize", CTLTYPE_QUAD }, \
>   	{ "availcpu", CTLTYPE_INT } \
>   }
>   
>   /*
>    * XXX This information should be moved to the man page.
>    *
>    * These are the support HW selectors for sysctlbyname.  Parameters that are byte counts or frequencies are 64 bit numbers.
>    * All other parameters are 32 bit numbers.
>    *
>    *   hw.memsize                - The number of bytes of physical memory in the system.
>    *
>    *   hw.ncpu                   - The maximum number of processors that could be available this boot.
>    *                               Use this value for sizing of static per processor arrays; i.e. processor load statistics.
>    *
>    *   hw.activecpu              - The number of processors currently available for executing threads.
>    *                               Use this number to determine the number threads to create in SMP aware applications.
>    *                               This number can change when power management modes are changed.
>    *
>    *   hw.physicalcpu            - The number of physical processors available in the current power management mode.
>    *   hw.physicalcpu_max        - The maximum number of physical processors that could be available this boot
>    *
>    *   hw.logicalcpu             - The number of logical processors available in the current power management mode.
>    *   hw.logicalcpu_max         - The maximum number of logical processors that could be available this boot
>    *
>    *   hw.tbfrequency            - This gives the time base frequency used by the OS and is the basis of all timing services.
>    *                               In general is is better to use mach's or higher level timing services, but this value
>    *                               is needed to convert the PPC Time Base registers to real time.
>    *
>    *   hw.cpufrequency           - These values provide the current, min and max cpu frequency.  The min and max are for
>    *   hw.cpufrequency_max       - all power management modes.  The current frequency is the max frequency in the current mode.
>    *   hw.cpufrequency_min       - All frequencies are in Hz.
>    *
>    *   hw.busfrequency           - These values provide the current, min and max bus frequency.  The min and max are for
>    *   hw.busfrequency_max       - all power management modes.  The current frequency is the max frequency in the current mode.
>    *   hw.busfrequency_min       - All frequencies are in Hz.
>    *
>    *   hw.cputype                - These values provide the mach-o cpu type and subtype.  A complete list is in <mach/machine.h>
>    *   hw.cpusubtype             - These values should be used to determine what processor family the running cpu is from so that
>    *                               the best binary can be chosen, or the best dynamic code generated.  They should not be used
>    *                               to determine if a given processor feature is available.
>    *   hw.cputhreadtype          - This value will be present if the processor supports threads.  Like hw.cpusubtype this selector
>    *                               should not be used to infer features, and only used to name the processors thread architecture.
>    *                               The values are defined in <mach/machine.h>
>    *
>    *   hw.byteorder              - Gives the byte order of the processor.  4321 for big endian, 1234 for little.
>    *
>    *   hw.pagesize               - Gives the size in bytes of the pages used by the processor and VM system.
>    *
>    *   hw.cachelinesize          - Gives the size in bytes of the processor's cache lines.
>    *                               This value should be use to control the strides of loops that use cache control instructions
>    *                               like dcbz, dcbt or dcbst.
>    *
>    *   hw.l1dcachesize           - These values provide the size in bytes of the L1, L2 and L3 caches.  If a cache is not present
>    *   hw.l1icachesize           - then the selector will return and error.
>    *   hw.l2cachesize            -
>    *   hw.l3cachesize            -
>    *
>    *   hw.packages               - Gives the number of processor packages.
>    *
>    * These are the selectors for optional processor features for specific processors.  Selectors that return errors are not support
>    * on the system.  Supported features will return 1 if they are recommended or 0 if they are supported but are not expected to help .
>    * performance.  Future versions of these selectors may return larger values as necessary so it is best to test for non zero.
>    *
>    * For PowerPC:
>    *
>    *   hw.optional.floatingpoint - Floating Point Instructions
>    *   hw.optional.altivec       - AltiVec Instructions
>    *   hw.optional.graphicsops   - Graphics Operations
>    *   hw.optional.64bitops      - 64-bit Instructions
>    *   hw.optional.fsqrt         - HW Floating Point Square Root Instruction
>    *   hw.optional.stfiwx        - Store Floating Point as Integer Word Indexed Instructions
>    *   hw.optional.dcba          - Data Cache Block Allocate Instruction
>    *   hw.optional.datastreams   - Data Streams Instructions
>    *   hw.optional.dcbtstreams   - Data Cache Block Touch Steams Instruction Form
>    *
>    * For x86 Architecture:
>    *
>    *   hw.optional.floatingpoint     - Floating Point Instructions
>    *   hw.optional.mmx               - Original MMX vector instructions
>    *   hw.optional.sse               - Streaming SIMD Extensions
>    *   hw.optional.sse2              - Streaming SIMD Extensions 2
>    *   hw.optional.sse3              - Streaming SIMD Extensions 3
>    *   hw.optional.supplementalsse3  - Supplemental Streaming SIMD Extensions 3
>    *   hw.optional.x86_64            - 64-bit support
>    */
>   
>   
>   /*
>    * CTL_USER definitions
>    */
>   #define	USER_CS_PATH		 1	/* string: _CS_PATH */
>   #define	USER_BC_BASE_MAX	 2	/* int: BC_BASE_MAX */
>   #define	USER_BC_DIM_MAX		 3	/* int: BC_DIM_MAX */
>   #define	USER_BC_SCALE_MAX	 4	/* int: BC_SCALE_MAX */
>   #define	USER_BC_STRING_MAX	 5	/* int: BC_STRING_MAX */
>   #define	USER_COLL_WEIGHTS_MAX	 6	/* int: COLL_WEIGHTS_MAX */
>   #define	USER_EXPR_NEST_MAX	 7	/* int: EXPR_NEST_MAX */
>   #define	USER_LINE_MAX		 8	/* int: LINE_MAX */
>   #define	USER_RE_DUP_MAX		 9	/* int: RE_DUP_MAX */
>   #define	USER_POSIX2_VERSION	10	/* int: POSIX2_VERSION */
>   #define	USER_POSIX2_C_BIND	11	/* int: POSIX2_C_BIND */
>   #define	USER_POSIX2_C_DEV	12	/* int: POSIX2_C_DEV */
>   #define	USER_POSIX2_CHAR_TERM	13	/* int: POSIX2_CHAR_TERM */
>   #define	USER_POSIX2_FORT_DEV	14	/* int: POSIX2_FORT_DEV */
>   #define	USER_POSIX2_FORT_RUN	15	/* int: POSIX2_FORT_RUN */
>   #define	USER_POSIX2_LOCALEDEF	16	/* int: POSIX2_LOCALEDEF */
>   #define	USER_POSIX2_SW_DEV	17	/* int: POSIX2_SW_DEV */
>   #define	USER_POSIX2_UPE		18	/* int: POSIX2_UPE */
>   #define	USER_STREAM_MAX		19	/* int: POSIX2_STREAM_MAX */
>   #define	USER_TZNAME_MAX		20	/* int: POSIX2_TZNAME_MAX */
>   #define	USER_MAXID		21	/* number of valid user ids */
>   
>   #define	CTL_USER_NAMES { \
>   	{ 0, 0 }, \
>   	{ "cs_path", CTLTYPE_STRING }, \
>   	{ "bc_base_max", CTLTYPE_INT }, \
>   	{ "bc_dim_max", CTLTYPE_INT }, \
>   	{ "bc_scale_max", CTLTYPE_INT }, \
>   	{ "bc_string_max", CTLTYPE_INT }, \
>   	{ "coll_weights_max", CTLTYPE_INT }, \
>   	{ "expr_nest_max", CTLTYPE_INT }, \
>   	{ "line_max", CTLTYPE_INT }, \
>   	{ "re_dup_max", CTLTYPE_INT }, \
>   	{ "posix2_version", CTLTYPE_INT }, \
>   	{ "posix2_c_bind", CTLTYPE_INT }, \
>   	{ "posix2_c_dev", CTLTYPE_INT }, \
>   	{ "posix2_char_term", CTLTYPE_INT }, \
>   	{ "posix2_fort_dev", CTLTYPE_INT }, \
>   	{ "posix2_fort_run", CTLTYPE_INT }, \
>   	{ "posix2_localedef", CTLTYPE_INT }, \
>   	{ "posix2_sw_dev", CTLTYPE_INT }, \
>   	{ "posix2_upe", CTLTYPE_INT }, \
>   	{ "stream_max", CTLTYPE_INT }, \
>   	{ "tzname_max", CTLTYPE_INT } \
>   }
>   
>   
>   
>   /*
>    * CTL_DEBUG definitions
>    *
>    * Second level identifier specifies which debug variable.
>    * Third level identifier specifies which stucture component.
>    */
>   #define	CTL_DEBUG_NAME		0	/* string: variable name */
>   #define	CTL_DEBUG_VALUE		1	/* int: variable value */
>   #define	CTL_DEBUG_MAXID		20
>   
>   
>   #if (CTL_MAXID != 9) || (KERN_MAXID != 72) || (VM_MAXID != 6) || (HW_MAXID != 26) || (USER_MAXID != 21) || (CTL_DEBUG_MAXID != 20)
>   #error Use the SYSCTL_*() macros and OID_AUTO instead!
>   #endif
>   
>   
>   
>   
>   
>   #endif /* SYSCTL_DEF_ENABLED */
>   
>   
>   #endif	/* !_SYS_SYSCTL_H_ */
>   
>   ```
>
>   
>
> * .c
>
>   ```
>   /*
>    * sysctl.c
>    * $Id: sysctl.c 120072 2014-05-14 22:29:40Z cal@macports.org $
>    *
>    * Copyright (c) 2009 The MacPorts Project
>    * All rights reserved.
>    *
>    * Redistribution and use in source and binary forms, with or without
>    * modification, are permitted provided that the following conditions
>    * are met:
>    * 1. Redistributions of source code must retain the above copyright
>    *    notice, this list of conditions and the following disclaimer.
>    * 2. Redistributions in binary form must reproduce the above copyright
>    *    notice, this list of conditions and the following disclaimer in the
>    *    documentation and/or other materials provided with the distribution.
>    * 3. Neither the name of The MacPorts Project nor the names of its contributors
>    *    may be used to endorse or promote products derived from this software
>    *    without specific prior written permission.
>    * 
>    * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
>    * AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
>    * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
>    * ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE
>    * LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
>    * CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
>    * SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
>    * INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
>    * CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
>    * ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
>    * POSSIBILITY OF SUCH DAMAGE.
>    */
>   
>   #if HAVE_CONFIG_H
>   #include <config.h>
>   #endif
>   
>   #include <tcl.h>
>   
>   #include <string.h>
>   #include <errno.h>
>   #include <sys/types.h>
>   
>   #if HAVE_SYS_SYSCTL_H
>   #include <sys/sysctl.h>
>   #endif
>   
>   #include "sysctl.h"
>   
>   /*
>    * Read-only wrapper for sysctlbyname(3). Only works for values of type CTLTYPE_INT and CTLTYPE_QUAD.
>    */
>   #ifdef HAVE_SYSCTLBYNAME
>   int SysctlCmd(ClientData clientData UNUSED, Tcl_Interp *interp, int objc, Tcl_Obj *CONST objv[])
>   #else
>   int SysctlCmd(ClientData clientData UNUSED, Tcl_Interp *interp, int objc UNUSED, Tcl_Obj *CONST objv[] UNUSED)
>   #endif
>   {
>   #ifdef HAVE_SYSCTLBYNAME
>       const char error_message[] = "sysctl failed: ";
>       Tcl_Obj *tcl_result;
>       int res;
>       char *name;
>       int value;
>       Tcl_WideInt long_value;
>       size_t len = sizeof(value);
>   
>       if (objc != 2) {
>           Tcl_WrongNumArgs(interp, 1, objv, "name");
>           return TCL_ERROR;
>       }
>   
>       name = Tcl_GetString(objv[1]);
>       res = sysctlbyname(name, &value, &len, NULL, 0);
>       if (res == -1 && errno != ENOMEM && errno != ERANGE) {
>           tcl_result = Tcl_NewStringObj(error_message, sizeof(error_message) - 1);
>           Tcl_AppendObjToObj(tcl_result, Tcl_NewStringObj(strerror(errno), -1));
>           Tcl_SetObjResult(interp, tcl_result);
>           return TCL_ERROR;
>       } else if (res == -1) {
>           len = sizeof(long_value);
>           res = sysctlbyname(name, &long_value, &len, NULL, 0);
>           if (res == -1) {
>               tcl_result = Tcl_NewStringObj(error_message, sizeof(error_message) - 1);
>               Tcl_AppendObjToObj(tcl_result, Tcl_NewStringObj(strerror(errno), -1));
>               Tcl_SetObjResult(interp, tcl_result);
>               return TCL_ERROR;
>           }
>           tcl_result = Tcl_NewWideIntObj(long_value);
>       } else {
>           tcl_result = Tcl_NewIntObj(value);
>       }
>       
>       Tcl_SetObjResult(interp, tcl_result);
>       return TCL_OK;
>   #else
>       Tcl_SetObjResult(interp, Tcl_NewStringObj("sysctl not available", -1));
>       return TCL_ERROR;
>   #endif
>   }
>   
>   ```
>
>   
>
> * .h

```
/*
 * Copyright (c) 2003-2004 Apple Computer, Inc. All rights reserved.
 *
 * @APPLE_OSREFERENCE_LICENSE_HEADER_START@
 * 
 * This file contains Original Code and/or Modifications of Original Code
 * as defined in and that are subject to the Apple Public Source License
 * Version 2.0 (the 'License'). You may not use this file except in
 * compliance with the License. The rights granted to you under the License
 * may not be used to create, or enable the creation or redistribution of,
 * unlawful or unlicensed copies of an Apple operating system, or to
 * circumvent, violate, or enable the circumvention or violation of, any
 * terms of an Apple operating system software license agreement.
 * 
 * Please obtain a copy of the License at
 * http://www.opensource.apple.com/apsl/ and read it before using this file.
 * 
 * The Original Code and all software distributed under the License are
 * distributed on an 'AS IS' basis, WITHOUT WARRANTY OF ANY KIND, EITHER
 * EXPRESS OR IMPLIED, AND APPLE HEREBY DISCLAIMS ALL SUCH WARRANTIES,
 * INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE, QUIET ENJOYMENT OR NON-INFRINGEMENT.
 * Please see the License for the specific language governing rights and
 * limitations under the License.
 * 
 * @APPLE_OSREFERENCE_LICENSE_HEADER_END@
 */

#ifndef	LIBKERN_SYSCTL_H
#define LIBKERN_SYSCTL_H

#include <sys/cdefs.h>

__BEGIN_DECLS

#include <sys/types.h>

/*
 * These are the support HW selectors for sysctlbyname.  Parameters that are byte counts or frequencies are 64 bit numbers.
 * All other parameters are 32 bit numbers.
 *
 *   hw.memsize                - The number of bytes of physical memory in the system.
 *
 *   hw.ncpu                   - The maximum number of processors that could be available this boot.
 *                               Use this value for sizing of static per processor arrays; i.e. processor load statistics.
 *
 *   hw.activecpu              - The number of processors currently available for executing threads.
 *                               Use this number to determine the number threads to create in SMP aware applications.
 *                               This number can change when power management modes are changed.
 *
 *   hw.physicalcpu            - The number of physical processors available in the current power management mode.
 *   hw.physicalcpu_max        - The maximum number of physical processors that could be available this boot
 *
 *   hw.logicalcpu             - The number of logical processors available in the current power management mode.
 *   hw.logicalcpu_max         - The maximum number of logical processors that could be available this boot
 *
 *   hw.tbfrequency            - This gives the time base frequency used by the OS and is the basis of all timing services.
 *                               In general is is better to use mach's or higher level timing services, but this value
 *                               is needed to convert the PPC Time Base registers to real time.
 *
 *   hw.cpufrequency           - These values provide the current, min and max cpu frequency.  The min and max are for
 *   hw.cpufrequency_max       - all power management modes.  The current frequency is the max frequency in the current mode.
 *   hw.cpufrequency_min       - All frequencies are in Hz.
 *
 *   hw.busfrequency           - These values provide the current, min and max bus frequency.  The min and max are for
 *   hw.busfrequency_max       - all power management modes.  The current frequency is the max frequency in the current mode.
 *   hw.busfrequency_min       - All frequencies are in Hz.
 *
 *   hw.cputype                - These values provide the mach-o cpu type and subtype.  A complete list is in <mach/machine.h>
 *   hw.cpusubtype             - These values should be used to determine what processor family the running cpu is from so that
 *                               the best binary can be chosen, or the best dynamic code generated.  They should not be used
 *                               to determine if a given processor feature is available.
 *   hw.cputhreadtype          - This value will be present if the processor supports threads.  Like hw.cpusubtype this selector
 *                               should not be used to infer features, and only used to name the processors thread architecture.
 *                               The values are defined in <mach/machine.h>
 *
 *   hw.byteorder              - Gives the byte order of the processor.  4321 for big endian, 1234 for little.
 *
 *   hw.pagesize               - Gives the size in bytes of the pages used by the processor and VM system.
 *
 *   hw.cachelinesize          - Gives the size in bytes of the processor's cache lines.
 *                               This value should be use to control the strides of loops that use cache control instructions
 *                               like dcbz, dcbt or dcbst.
 *
 *   hw.l1dcachesize           - These values provide the size in bytes of the L1, L2 and L3 caches.  If a cache is not present
 *   hw.l1icachesize           - then the selector will return and error.
 *   hw.l2cachesize            -
 *   hw.l3cachesize            -
 *
 *
 * These are the selectors for optional processor features.  Selectors that return errors are not support on the system.
 * Supported features will return 1 if they are recommended or 0 if they are supported but are not expected to help performance.
 * Future versions of these selectors may return larger values as necessary so it is best to test for non zero.
 *
 *   hw.optional.floatingpoint - Floating Point Instructions
 *   hw.optional.altivec       - AltiVec Instructions
 *   hw.optional.graphicsops   - Graphics Operations
 *   hw.optional.64bitops      - 64-bit Instructions
 *   hw.optional.fsqrt         - HW Floating Point Square Root Instruction
 *   hw.optional.stfiwx        - Store Floating Point as Integer Word Indexed Instructions
 *   hw.optional.dcba          - Data Cache Block Allocate Instruction
 *   hw.optional.datastreams   - Data Streams Instructions
 *   hw.optional.dcbtstreams   - Data Cache Block Touch Steams Instruction Form
 *
 */

/*
 * Sysctl handling
 */
int     sysctlbyname(const char *, void *, size_t *, void *, size_t);

__END_DECLS

#endif	/* LIBKERN_SYSCTL_H */

```



#### sysinfo.cc





```
// Copyright 2015 Google Inc. All rights reserved.
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//     http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

#include "sysinfo.h"
#include "internal_macros.h"

#ifdef BENCHMARK_OS_WINDOWS
#include <Shlwapi.h>
#include <VersionHelpers.h>
#include <Windows.h>
#else
#include <fcntl.h>
#include <sys/resource.h>
#include <sys/time.h>
#include <sys/types.h>  // this header must be included before 'sys/sysctl.h' to avoid compilation error on FreeBSD
#include <unistd.h>
#if defined BENCHMARK_OS_FREEBSD || defined BENCHMARK_OS_MACOSX
#include <sys/sysctl.h>
#endif
#endif

#include <cerrno>
#include <cstdint>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>
#include <limits>
#include <mutex>

#include "arraysize.h"
#include "check.h"
#include "cycleclock.h"
#include "internal_macros.h"
#include "log.h"
#include "sleep.h"
#include "string_util.h"

namespace benchmark {
namespace {
std::once_flag cpuinfo_init;
double cpuinfo_cycles_per_second = 1.0;
int cpuinfo_num_cpus = 1;  // Conservative guess

#if !defined BENCHMARK_OS_MACOSX
const int64_t estimate_time_ms = 1000;

// Helper function estimates cycles/sec by observing cycles elapsed during
// sleep(). Using small sleep time decreases accuracy significantly.
int64_t EstimateCyclesPerSecond() {
  const int64_t start_ticks = cycleclock::Now();
  SleepForMilliseconds(estimate_time_ms);
  return cycleclock::Now() - start_ticks;
}
#endif

#if defined BENCHMARK_OS_LINUX || defined BENCHMARK_OS_CYGWIN
// Helper function for reading an int from a file. Returns true if successful
// and the memory location pointed to by value is set to the value read.
bool ReadIntFromFile(const char* file, long* value) {
  bool ret = false;
  int fd = open(file, O_RDONLY);
  if (fd != -1) {
    char line[1024];
    char* err;
    memset(line, '\0', sizeof(line));
    ssize_t read_err = read(fd, line, sizeof(line) - 1);
    ((void)read_err); // prevent unused warning
    CHECK(read_err >= 0);
    const long temp_value = strtol(line, &err, 10);
    if (line[0] != '\0' && (*err == '\n' || *err == '\0')) {
      *value = temp_value;
      ret = true;
    }
    close(fd);
  }
  return ret;
}
#endif

#if defined BENCHMARK_OS_LINUX || defined BENCHMARK_OS_CYGWIN
static std::string convertToLowerCase(std::string s) {
  for (auto& ch : s)
    ch = std::tolower(ch);
  return s;
}
static bool startsWithKey(std::string Value, std::string Key,
                          bool IgnoreCase = true) {
  if (IgnoreCase) {
    Key = convertToLowerCase(std::move(Key));
    Value = convertToLowerCase(std::move(Value));
  }
  return Value.compare(0, Key.size(), Key) == 0;
}
#endif

void InitializeSystemInfo() {
#if defined BENCHMARK_OS_LINUX || defined BENCHMARK_OS_CYGWIN
  char line[1024];
  char* err;
  long freq;

  bool saw_mhz = false;

  // If the kernel is exporting the tsc frequency use that. There are issues
  // where cpuinfo_max_freq cannot be relied on because the BIOS may be
  // exporintg an invalid p-state (on x86) or p-states may be used to put the
  // processor in a new mode (turbo mode). Essentially, those frequencies
  // cannot always be relied upon. The same reasons apply to /proc/cpuinfo as
  // well.
  if (!saw_mhz &&
      ReadIntFromFile("/sys/devices/system/cpu/cpu0/tsc_freq_khz", &freq)) {
    // The value is in kHz (as the file name suggests).  For example, on a
    // 2GHz warpstation, the file contains the value "2000000".
    cpuinfo_cycles_per_second = freq * 1000.0;
    saw_mhz = true;
  }

  // If CPU scaling is in effect, we want to use the *maximum* frequency,
  // not whatever CPU speed some random processor happens to be using now.
  if (!saw_mhz &&
      ReadIntFromFile("/sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_max_freq",
                      &freq)) {
    // The value is in kHz.  For example, on a 2GHz warpstation, the file
    // contains the value "2000000".
    cpuinfo_cycles_per_second = freq * 1000.0;
    saw_mhz = true;
  }

  // Read /proc/cpuinfo for other values, and if there is no cpuinfo_max_freq.
  const char* pname = "/proc/cpuinfo";
  int fd = open(pname, O_RDONLY);
  if (fd == -1) {
    perror(pname);
    if (!saw_mhz) {
      cpuinfo_cycles_per_second =
          static_cast<double>(EstimateCyclesPerSecond());
    }
    return;
  }

  double bogo_clock = 1.0;
  bool saw_bogo = false;
  long max_cpu_id = 0;
  int num_cpus = 0;
  line[0] = line[1] = '\0';
  size_t chars_read = 0;
  do {  // we'll exit when the last read didn't read anything
    // Move the next line to the beginning of the buffer
    const size_t oldlinelen = strlen(line);
    if (sizeof(line) == oldlinelen + 1)  // oldlinelen took up entire line
      line[0] = '\0';
    else  // still other lines left to save
      memmove(line, line + oldlinelen + 1, sizeof(line) - (oldlinelen + 1));
    // Terminate the new line, reading more if we can't find the newline
    char* newline = strchr(line, '\n');
    if (newline == nullptr) {
      const size_t linelen = strlen(line);
      const size_t bytes_to_read = sizeof(line) - 1 - linelen;
      CHECK(bytes_to_read > 0);  // because the memmove recovered >=1 bytes
      chars_read = read(fd, line + linelen, bytes_to_read);
      line[linelen + chars_read] = '\0';
      newline = strchr(line, '\n');
    }
    if (newline != nullptr) *newline = '\0';

    // When parsing the "cpu MHz" and "bogomips" (fallback) entries, we only
    // accept postive values. Some environments (virtual machines) report zero,
    // which would cause infinite looping in WallTime_Init.
    if (!saw_mhz && startsWithKey(line, "cpu MHz")) {
      const char* freqstr = strchr(line, ':');
      if (freqstr) {
        cpuinfo_cycles_per_second = strtod(freqstr + 1, &err) * 1000000.0;
        if (freqstr[1] != '\0' && *err == '\0' && cpuinfo_cycles_per_second > 0)
          saw_mhz = true;
      }
    } else if (startsWithKey(line, "bogomips")) {
      const char* freqstr = strchr(line, ':');
      if (freqstr) {
        bogo_clock = strtod(freqstr + 1, &err) * 1000000.0;
        if (freqstr[1] != '\0' && *err == '\0' && bogo_clock > 0)
          saw_bogo = true;
      }
    } else if (startsWithKey(line, "processor", /*IgnoreCase*/false)) {
      // The above comparison is case-sensitive because ARM kernels often
      // include a "Processor" line that tells you about the CPU, distinct
      // from the usual "processor" lines that give you CPU ids. No current
      // Linux architecture is using "Processor" for CPU ids.
      num_cpus++;  // count up every time we see an "processor :" entry
      const char* id_str = strchr(line, ':');
      if (id_str) {
        const long cpu_id = strtol(id_str + 1, &err, 10);
        if (id_str[1] != '\0' && *err == '\0' && max_cpu_id < cpu_id)
          max_cpu_id = cpu_id;
      }
    }
  } while (chars_read > 0);
  close(fd);

  if (!saw_mhz) {
    if (saw_bogo) {
      // If we didn't find anything better, we'll use bogomips, but
      // we're not happy about it.
      cpuinfo_cycles_per_second = bogo_clock;
    } else {
      // If we don't even have bogomips, we'll use the slow estimation.
      cpuinfo_cycles_per_second =
          static_cast<double>(EstimateCyclesPerSecond());
    }
  }
  if (num_cpus == 0) {
    fprintf(stderr, "Failed to read num. CPUs correctly from /proc/cpuinfo\n");
  } else {
    if ((max_cpu_id + 1) != num_cpus) {
      fprintf(stderr,
              "CPU ID assignments in /proc/cpuinfo seem messed up."
              " This is usually caused by a bad BIOS.\n");
    }
    cpuinfo_num_cpus = num_cpus;
  }

#elif defined BENCHMARK_OS_FREEBSD
// For this sysctl to work, the machine must be configured without
// SMP, APIC, or APM support.  hz should be 64-bit in freebsd 7.0
// and later.  Before that, it's a 32-bit quantity (and gives the
// wrong answer on machines faster than 2^32 Hz).  See
//  http://lists.freebsd.org/pipermail/freebsd-i386/2004-November/001846.html
// But also compare FreeBSD 7.0:
//  http://fxr.watson.org/fxr/source/i386/i386/tsc.c?v=RELENG70#L223
//  231         error = sysctl_handle_quad(oidp, &freq, 0, req);
// To FreeBSD 6.3 (it's the same in 6-STABLE):
//  http://fxr.watson.org/fxr/source/i386/i386/tsc.c?v=RELENG6#L131
//  139         error = sysctl_handle_int(oidp, &freq, sizeof(freq), req);
#if __FreeBSD__ >= 7
  uint64_t hz = 0;
#else
  unsigned int hz = 0;
#endif
  size_t sz = sizeof(hz);
  const char* sysctl_path = "machdep.tsc_freq";
  if (sysctlbyname(sysctl_path, &hz, &sz, nullptr, 0) != 0) {
    fprintf(stderr, "Unable to determine clock rate from sysctl: %s: %s\n",
            sysctl_path, strerror(errno));
    cpuinfo_cycles_per_second = static_cast<double>(EstimateCyclesPerSecond());
  } else {
    cpuinfo_cycles_per_second = hz;
  }
// TODO: also figure out cpuinfo_num_cpus

#elif defined BENCHMARK_OS_WINDOWS
  // In NT, read MHz from the registry. If we fail to do so or we're in win9x
  // then make a crude estimate.
  DWORD data, data_size = sizeof(data);
  if (IsWindowsXPOrGreater() &&
      SUCCEEDED(
          SHGetValueA(HKEY_LOCAL_MACHINE,
                      "HARDWARE\\DESCRIPTION\\System\\CentralProcessor\\0",
                      "~MHz", nullptr, &data, &data_size)))
    cpuinfo_cycles_per_second =
        static_cast<double>((int64_t)data * (int64_t)(1000 * 1000));  // was mhz
  else
    cpuinfo_cycles_per_second = static_cast<double>(EstimateCyclesPerSecond());

  SYSTEM_INFO sysinfo;
  // Use memset as opposed to = {} to avoid GCC missing initializer false
  // positives.
  std::memset(&sysinfo, 0, sizeof(SYSTEM_INFO));
  GetSystemInfo(&sysinfo);
  cpuinfo_num_cpus = sysinfo.dwNumberOfProcessors;  // number of logical
                                                    // processors in the current
                                                    // group

#elif defined BENCHMARK_OS_MACOSX
  int32_t num_cpus = 0;
  size_t size = sizeof(num_cpus);
  if (::sysctlbyname("hw.ncpu", &num_cpus, &size, nullptr, 0) == 0 &&
      (size == sizeof(num_cpus))) {
    cpuinfo_num_cpus = num_cpus;
  } else {
    fprintf(stderr, "%s\n", strerror(errno));
    std::exit(EXIT_FAILURE);
  }
  int64_t cpu_freq = 0;
  size = sizeof(cpu_freq);
  if (::sysctlbyname("hw.cpufrequency", &cpu_freq, &size, nullptr, 0) == 0 &&
      (size == sizeof(cpu_freq))) {
    cpuinfo_cycles_per_second = cpu_freq;
  } else {
    #if defined BENCHMARK_OS_IOS
    fprintf(stderr, "CPU frequency cannot be detected. \n");
    cpuinfo_cycles_per_second = 0;
    #else
    fprintf(stderr, "%s\n", strerror(errno));
    std::exit(EXIT_FAILURE);
    #endif
  }
#else
  // Generic cycles per second counter
  cpuinfo_cycles_per_second = static_cast<double>(EstimateCyclesPerSecond());
#endif
}

}  // end namespace

double CyclesPerSecond(void) {
  std::call_once(cpuinfo_init, InitializeSystemInfo);
  return cpuinfo_cycles_per_second;
}

int NumCPUs(void) {
  std::call_once(cpuinfo_init, InitializeSystemInfo);
  return cpuinfo_num_cpus;
}

// The ""'s catch people who don't pass in a literal for "str"
#define strliterallen(str) (sizeof("" str "") - 1)

// Must use a string literal for prefix.
#define memprefix(str, len, prefix)                       \
  ((((len) >= strliterallen(prefix)) &&                   \
    std::memcmp(str, prefix, strliterallen(prefix)) == 0) \
       ? str + strliterallen(prefix)                      \
       : nullptr)

bool CpuScalingEnabled() {
#ifndef BENCHMARK_OS_WINDOWS
  // On Linux, the CPUfreq subsystem exposes CPU information as files on the
  // local file system. If reading the exported files fails, then we may not be
  // running on Linux, so we silently ignore all the read errors.
  for (int cpu = 0, num_cpus = NumCPUs(); cpu < num_cpus; ++cpu) {
    std::string governor_file =
        StrCat("/sys/devices/system/cpu/cpu", cpu, "/cpufreq/scaling_governor");
    FILE* file = fopen(governor_file.c_str(), "r");
    if (!file) break;
    char buff[16];
    size_t bytes_read = fread(buff, 1, sizeof(buff), file);
    fclose(file);
    if (memprefix(buff, bytes_read, "performance") == nullptr) return true;
  }
#endif
  return false;
}

}  // end namespace benchmark

```



#### uname.c

```
/* uname -- print system information

   Copyright (C) 1989-2017 Free Software Foundation, Inc.

   This program is free software: you can redistribute it and/or modify
   it under the terms of the GNU General Public License as published by
   the Free Software Foundation, either version 3 of the License, or
   (at your option) any later version.

   This program is distributed in the hope that it will be useful,
   but WITHOUT ANY WARRANTY; without even the implied warranty of
   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
   GNU General Public License for more details.

   You should have received a copy of the GNU General Public License
   along with this program.  If not, see <http://www.gnu.org/licenses/>.  */

/* Written by David MacKenzie <djm@gnu.ai.mit.edu> */

#include <config.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/utsname.h>
#include <getopt.h>

#if HAVE_SYSINFO && HAVE_SYS_SYSTEMINFO_H
# include <sys/systeminfo.h>
#endif

#if HAVE_SYS_SYSCTL_H
# if HAVE_SYS_PARAM_H
#  include <sys/param.h> /* needed for OpenBSD 3.0 */
# endif
# include <sys/sysctl.h>
# ifdef HW_MODEL
#  ifdef HW_MACHINE_ARCH
/* E.g., FreeBSD 4.5, NetBSD 1.5.2 */
#   define UNAME_HARDWARE_PLATFORM HW_MODEL
#   define UNAME_PROCESSOR HW_MACHINE_ARCH
#  else
/* E.g., OpenBSD 3.0 */
#   define UNAME_PROCESSOR HW_MODEL
#  endif
# endif
#endif

#ifdef __APPLE__
# include <mach/machine.h>
# include <mach-o/arch.h>
#endif

#include "system.h"
#include "die.h"
#include "error.h"
#include "quote.h"
#include "uname.h"

/* The official name of this program (e.g., no 'g' prefix).  */
#define PROGRAM_NAME (uname_mode == UNAME_UNAME ? "uname" : "arch")

#define AUTHORS proper_name ("David MacKenzie")
#define ARCH_AUTHORS "David MacKenzie", "Karel Zak"

/* Values that are bitwise or'd into 'toprint'. */
/* Kernel name. */
#define PRINT_KERNEL_NAME 1

/* Node name on a communications network. */
#define PRINT_NODENAME 2

/* Kernel release. */
#define PRINT_KERNEL_RELEASE 4

/* Kernel version. */
#define PRINT_KERNEL_VERSION 8

/* Machine hardware name. */
#define PRINT_MACHINE 16

/* Processor type. */
#define PRINT_PROCESSOR 32

/* Hardware platform.  */
#define PRINT_HARDWARE_PLATFORM 64

/* Operating system.  */
#define PRINT_OPERATING_SYSTEM 128

static struct option const uname_long_options[] =
{
  {"all", no_argument, NULL, 'a'},
  {"kernel-name", no_argument, NULL, 's'},
  {"sysname", no_argument, NULL, 's'},	/* Obsolescent.  */
  {"nodename", no_argument, NULL, 'n'},
  {"kernel-release", no_argument, NULL, 'r'},
  {"release", no_argument, NULL, 'r'},  /* Obsolescent.  */
  {"kernel-version", no_argument, NULL, 'v'},
  {"machine", no_argument, NULL, 'm'},
  {"processor", no_argument, NULL, 'p'},
  {"hardware-platform", no_argument, NULL, 'i'},
  {"operating-system", no_argument, NULL, 'o'},
  {GETOPT_HELP_OPTION_DECL},
  {GETOPT_VERSION_OPTION_DECL},
  {NULL, 0, NULL, 0}
};

static struct option const arch_long_options[] =
{
  {GETOPT_HELP_OPTION_DECL},
  {GETOPT_VERSION_OPTION_DECL},
  {NULL, 0, NULL, 0}
};

void
usage (int status)
{
  if (status != EXIT_SUCCESS)
    emit_try_help ();
  else
    {
      printf (_("Usage: %s [OPTION]...\n"), program_name);

      if (uname_mode == UNAME_UNAME)
        {
          fputs (_("\
Print certain system information.  With no OPTION, same as -s.\n\
\n\
  -a, --all                print all information, in the following order,\n\
                             except omit -p and -i if unknown:\n\
  -s, --kernel-name        print the kernel name\n\
  -n, --nodename           print the network node hostname\n\
  -r, --kernel-release     print the kernel release\n\
"), stdout);
          fputs (_("\
  -v, --kernel-version     print the kernel version\n\
  -m, --machine            print the machine hardware name\n\
  -p, --processor          print the processor type (non-portable)\n\
  -i, --hardware-platform  print the hardware platform (non-portable)\n\
  -o, --operating-system   print the operating system\n\
"), stdout);
        }
      else
        {
          fputs (_("\
Print machine architecture.\n\
\n\
"), stdout);
        }

      fputs (HELP_OPTION_DESCRIPTION, stdout);
      fputs (VERSION_OPTION_DESCRIPTION, stdout);
      emit_ancillary_info (PROGRAM_NAME);
    }
  exit (status);
}

/* Print ELEMENT, preceded by a space if something has already been
   printed.  */

static void
print_element (char const *element)
{
  static bool printed;
  if (printed)
    putchar (' ');
  printed = true;
  fputs (element, stdout);
}


/* Set all the option flags according to the switches specified.
   Return the mask indicating which elements to print.  */

static int
decode_switches (int argc, char **argv)
{
  int c;
  unsigned int toprint = 0;

  if (uname_mode == UNAME_ARCH)
    {
      while ((c = getopt_long (argc, argv, "",
                               arch_long_options, NULL)) != -1)
        {
          switch (c)
            {
            case_GETOPT_HELP_CHAR;

            case_GETOPT_VERSION_CHAR (PROGRAM_NAME, ARCH_AUTHORS);

            default:
              usage (EXIT_FAILURE);
            }
        }
      toprint = PRINT_MACHINE;
    }
  else
    {
      while ((c = getopt_long (argc, argv, "asnrvmpio",
                               uname_long_options, NULL)) != -1)
        {
          switch (c)
            {
            case 'a':
              toprint = UINT_MAX;
              break;

            case 's':
              toprint |= PRINT_KERNEL_NAME;
              break;

            case 'n':
              toprint |= PRINT_NODENAME;
              break;

            case 'r':
              toprint |= PRINT_KERNEL_RELEASE;
              break;

            case 'v':
              toprint |= PRINT_KERNEL_VERSION;
              break;

            case 'm':
              toprint |= PRINT_MACHINE;
              break;

            case 'p':
              toprint |= PRINT_PROCESSOR;
              break;

            case 'i':
              toprint |= PRINT_HARDWARE_PLATFORM;
              break;

            case 'o':
              toprint |= PRINT_OPERATING_SYSTEM;
              break;

            case_GETOPT_HELP_CHAR;

            case_GETOPT_VERSION_CHAR (PROGRAM_NAME, AUTHORS);

            default:
              usage (EXIT_FAILURE);
            }
        }
    }

  if (argc != optind)
    {
      error (0, 0, _("extra operand %s"), quote (argv[optind]));
      usage (EXIT_FAILURE);
    }

  return toprint;
}

int
main (int argc, char **argv)
{
  static char const unknown[] = "unknown";

  /* Mask indicating which elements to print. */
  unsigned int toprint = 0;

  initialize_main (&argc, &argv);
  set_program_name (argv[0]);
  setlocale (LC_ALL, "");
  bindtextdomain (PACKAGE, LOCALEDIR);
  textdomain (PACKAGE);

  atexit (close_stdout);

  toprint = decode_switches (argc, argv);

  if (toprint == 0)
    toprint = PRINT_KERNEL_NAME;

  if (toprint
       & (PRINT_KERNEL_NAME | PRINT_NODENAME | PRINT_KERNEL_RELEASE
          | PRINT_KERNEL_VERSION | PRINT_MACHINE))
    {
      struct utsname name;

      if (uname (&name) == -1)
        die (EXIT_FAILURE, errno, _("cannot get system name"));

      if (toprint & PRINT_KERNEL_NAME)
        print_element (name.sysname);
      if (toprint & PRINT_NODENAME)
        print_element (name.nodename);
      if (toprint & PRINT_KERNEL_RELEASE)
        print_element (name.release);
      if (toprint & PRINT_KERNEL_VERSION)
        print_element (name.version);
      if (toprint & PRINT_MACHINE)
        print_element (name.machine);
    }

  if (toprint & PRINT_PROCESSOR)
    {
      char const *element = unknown;
#if HAVE_SYSINFO && defined SI_ARCHITECTURE
      {
        static char processor[257];
        if (0 <= sysinfo (SI_ARCHITECTURE, processor, sizeof processor))
          element = processor;
      }
#endif
#ifdef UNAME_PROCESSOR
      if (element == unknown)
        {
          static char processor[257];
          size_t s = sizeof processor;
          static int mib[] = { CTL_HW, UNAME_PROCESSOR };
          if (sysctl (mib, 2, processor, &s, 0, 0) >= 0)
            element = processor;

# ifdef __APPLE__
          /* This kludge works around a bug in Mac OS X.  */
          if (element == unknown)
            {
              cpu_type_t cputype;
              size_t cs = sizeof cputype;
              NXArchInfo const *ai;
              if (sysctlbyname ("hw.cputype", &cputype, &cs, NULL, 0) == 0
                  && (ai = NXGetArchInfoFromCpuType (cputype,
                                                     CPU_SUBTYPE_MULTIPLE))
                  != NULL)
                element = ai->name;

              /* Hack "safely" around the ppc vs. powerpc return value. */
              if (cputype == CPU_TYPE_POWERPC
                  && STRNCMP_LIT (element, "ppc") == 0)
                element = "powerpc";
            }
# endif
        }
#endif
      if (! (toprint == UINT_MAX && element == unknown))
        print_element (element);
    }

  if (toprint & PRINT_HARDWARE_PLATFORM)
    {
      char const *element = unknown;
#if HAVE_SYSINFO && defined SI_PLATFORM
      {
        static char hardware_platform[257];
        if (0 <= sysinfo (SI_PLATFORM,
                          hardware_platform, sizeof hardware_platform))
          element = hardware_platform;
      }
#endif
#ifdef UNAME_HARDWARE_PLATFORM
      if (element == unknown)
        {
          static char hardware_platform[257];
          size_t s = sizeof hardware_platform;
          static int mib[] = { CTL_HW, UNAME_HARDWARE_PLATFORM };
          if (sysctl (mib, 2, hardware_platform, &s, 0, 0) >= 0)
            element = hardware_platform;
        }
#endif
      if (! (toprint == UINT_MAX && element == unknown))
        print_element (element);
    }

  if (toprint & PRINT_OPERATING_SYSTEM)
    print_element (HOST_OPERATING_SYSTEM);

  putchar ('\n');

  return EXIT_SUCCESS;
}

```



#### timing

```
#include <mach/mach_time.h>
#include <stdint.h>
#include <stdlib.h>

double intervalInCycles( uint64_t startTime, uint64_t endTime )
{
	uint64_t rawTime = endTime - startTime;
	static double conversion = 0.0;
	
	if( 0.0 == conversion )
	{
		mach_timebase_info_data_t	info;
		kern_return_t err = mach_timebase_info( &info );
		if( 0 != err )
			return 0;
		
		uint64_t freq = 0;
		size_t freqSize = sizeof( freq );
		int err2 = sysctlbyname( "hw.cpufrequency", &freq, &freqSize, NULL, 0L );
		if( 0 != err2 )
			return 0;
		
		conversion = (double) freq * (1e-9 * (double) info.numer / (double) info.denom);
	}
	
	return (double) rawTime * conversion;
}


```



####  #import "UIDevice-Hardware.h" 

>* UIDevice-Hardware.h
>
>  ```
>  /*
>   Erica Sadun, http://ericasadun.com
>   iPhone Developer's Cookbook, 6.x Edition
>   BSD License, Use at your own risk
>   */
>  
>  #import <UIKit/UIKit.h>
>  
>  #define IFPGA_NAMESTRING                @"iFPGA"
>  
>  #define IPHONE_1G_NAMESTRING            @"iPhone 1G"
>  #define IPHONE_3G_NAMESTRING            @"iPhone 3G"
>  #define IPHONE_3GS_NAMESTRING           @"iPhone 3GS" 
>  #define IPHONE_4_NAMESTRING             @"iPhone 4" 
>  #define IPHONE_4S_NAMESTRING            @"iPhone 4S"
>  #define IPHONE_5_NAMESTRING             @"iPhone 5"
>  #define IPHONE_UNKNOWN_NAMESTRING       @"Unknown iPhone"
>  
>  #define IPOD_1G_NAMESTRING              @"iPod touch 1G"
>  #define IPOD_2G_NAMESTRING              @"iPod touch 2G"
>  #define IPOD_3G_NAMESTRING              @"iPod touch 3G"
>  #define IPOD_4G_NAMESTRING              @"iPod touch 4G"
>  #define IPOD_UNKNOWN_NAMESTRING         @"Unknown iPod"
>  
>  #define IPAD_1G_NAMESTRING              @"iPad 1G"
>  #define IPAD_2G_NAMESTRING              @"iPad 2G"
>  #define IPAD_3G_NAMESTRING              @"iPad 3G"
>  #define IPAD_4G_NAMESTRING              @"iPad 4G"
>  #define IPAD_UNKNOWN_NAMESTRING         @"Unknown iPad"
>  
>  #define APPLETV_2G_NAMESTRING           @"Apple TV 2G"
>  #define APPLETV_3G_NAMESTRING           @"Apple TV 3G"
>  #define APPLETV_4G_NAMESTRING           @"Apple TV 4G"
>  #define APPLETV_UNKNOWN_NAMESTRING      @"Unknown Apple TV"
>  
>  #define IOS_FAMILY_UNKNOWN_DEVICE       @"Unknown iOS device"
>  
>  #define SIMULATOR_NAMESTRING            @"iPhone Simulator"
>  #define SIMULATOR_IPHONE_NAMESTRING     @"iPhone Simulator"
>  #define SIMULATOR_IPAD_NAMESTRING       @"iPad Simulator"
>  #define SIMULATOR_APPLETV_NAMESTRING    @"Apple TV Simulator" // :)
>  
>  typedef enum {
>      UIDeviceUnknown,
>      
>      UIDeviceSimulator,
>      UIDeviceSimulatoriPhone,
>      UIDeviceSimulatoriPad,
>      UIDeviceSimulatorAppleTV,
>      
>      UIDevice1GiPhone,
>      UIDevice3GiPhone,
>      UIDevice3GSiPhone,
>      UIDevice4iPhone,
>      UIDevice4SiPhone,
>      UIDevice5iPhone,
>      
>      UIDevice1GiPod,
>      UIDevice2GiPod,
>      UIDevice3GiPod,
>      UIDevice4GiPod,
>      
>      UIDevice1GiPad,
>      UIDevice2GiPad,
>      UIDevice3GiPad,
>      UIDevice4GiPad,
>      
>      UIDeviceAppleTV2,
>      UIDeviceAppleTV3,
>      UIDeviceAppleTV4,
>      
>      UIDeviceUnknowniPhone,
>      UIDeviceUnknowniPod,
>      UIDeviceUnknowniPad,
>      UIDeviceUnknownAppleTV,
>      UIDeviceIFPGA,
>  
>  } UIDevicePlatform;
>  
>  typedef enum {
>      UIDeviceFamilyiPhone,
>      UIDeviceFamilyiPod,
>      UIDeviceFamilyiPad,
>      UIDeviceFamilyAppleTV,
>      UIDeviceFamilyUnknown,
>      
>  } UIDeviceFamily;
>  
>  @interface UIDevice (Hardware)
>  - (NSString *) platform;
>  - (NSString *) hwmodel;
>  - (NSUInteger) platformType;
>  - (NSString *) platformString;
>  
>  - (NSUInteger) cpuFrequency;
>  - (NSUInteger) busFrequency;
>  - (NSUInteger) cpuCount;
>  - (NSUInteger) totalMemory;
>  - (NSUInteger) userMemory;
>  
>  - (NSNumber *) totalDiskSpace;
>  - (NSNumber *) freeDiskSpace;
>  
>  - (NSString *) macaddress;
>  
>  - (BOOL) hasRetinaDisplay;
>  - (UIDeviceFamily) deviceFamily;
>  @end
>  
>  ```
>
>  

```
/*
 Erica Sadun, http://ericasadun.com
 iPhone Developer's Cookbook, 6.x Edition
 BSD License, Use at your own risk
 */

// Thanks to Emanuele Vulcano, Kevin Ballard/Eridius, Ryandjohnson, Matt Brown, etc.

#include <sys/socket.h> // Per msqr
#include <sys/sysctl.h>
#include <net/if.h>
#include <net/if_dl.h>

#import "UIDevice-Hardware.h"

@implementation UIDevice (Hardware)
/*
 Platforms
 
 iFPGA ->        ??

 iPhone1,1 ->    iPhone 1G, M68
 iPhone1,2 ->    iPhone 3G, N82
 iPhone2,1 ->    iPhone 3GS, N88
 iPhone3,1 ->    iPhone 4/AT&T, N89
 iPhone3,2 ->    iPhone 4/Other Carrier?, ??
 iPhone3,3 ->    iPhone 4/Verizon, TBD
 iPhone4,1 ->    (iPhone 4S/GSM), TBD
 iPhone4,2 ->    (iPhone 4S/CDMA), TBD
 iPhone4,3 ->    (iPhone 4S/???)
 iPhone5,1 ->    iPhone Next Gen, TBD
 iPhone5,1 ->    iPhone Next Gen, TBD
 iPhone5,1 ->    iPhone Next Gen, TBD

 iPod1,1   ->    iPod touch 1G, N45
 iPod2,1   ->    iPod touch 2G, N72
 iPod2,2   ->    Unknown, ??
 iPod3,1   ->    iPod touch 3G, N18
 iPod4,1   ->    iPod touch 4G, N80
 
 // Thanks NSForge
 iPad1,1   ->    iPad 1G, WiFi and 3G, K48
 iPad2,1   ->    iPad 2G, WiFi, K93
 iPad2,2   ->    iPad 2G, GSM 3G, K94
 iPad2,3   ->    iPad 2G, CDMA 3G, K95
 iPad3,1   ->    (iPad 3G, WiFi)
 iPad3,2   ->    (iPad 3G, GSM)
 iPad3,3   ->    (iPad 3G, CDMA)
 iPad4,1   ->    (iPad 4G, WiFi)
 iPad4,2   ->    (iPad 4G, GSM)
 iPad4,3   ->    (iPad 4G, CDMA)

 AppleTV2,1 ->   AppleTV 2, K66
 AppleTV3,1 ->   AppleTV 3, ??

 i386, x86_64 -> iPhone Simulator
*/


#pragma mark sysctlbyname utils
- (NSString *) getSysInfoByName:(char *)typeSpecifier
{
    size_t size;
    sysctlbyname(typeSpecifier, NULL, &size, NULL, 0);
    
    char *answer = malloc(size);
    sysctlbyname(typeSpecifier, answer, &size, NULL, 0);
    
    NSString *results = [NSString stringWithCString:answer encoding: NSUTF8StringEncoding];

    free(answer);
    return results;
}

- (NSString *) platform
{
    return [self getSysInfoByName:"hw.machine"];
}


// Thanks, Tom Harrington (Atomicbird)
- (NSString *) hwmodel
{
    return [self getSysInfoByName:"hw.model"];
}

#pragma mark sysctl utils
- (NSUInteger) getSysInfo: (uint) typeSpecifier
{
    size_t size = sizeof(int);
    int results;
    int mib[2] = {CTL_HW, typeSpecifier};
    sysctl(mib, 2, &results, &size, NULL, 0);
    return (NSUInteger) results;
}

- (NSUInteger) cpuFrequency
{
    return [self getSysInfo:HW_CPU_FREQ];
}

- (NSUInteger) busFrequency
{
    return [self getSysInfo:HW_BUS_FREQ];
}

- (NSUInteger) cpuCount
{
    return [self getSysInfo:HW_NCPU];
}

- (NSUInteger) totalMemory
{
    return [self getSysInfo:HW_PHYSMEM];
}

- (NSUInteger) userMemory
{
    return [self getSysInfo:HW_USERMEM];
}

- (NSUInteger) maxSocketBufferSize
{
    return [self getSysInfo:KIPC_MAXSOCKBUF];
}

#pragma mark file system -- Thanks Joachim Bean!

/*
 extern NSString *NSFileSystemSize;
 extern NSString *NSFileSystemFreeSize;
 extern NSString *NSFileSystemNodes;
 extern NSString *NSFileSystemFreeNodes;
 extern NSString *NSFileSystemNumber;
*/

- (NSNumber *) totalDiskSpace
{
    NSDictionary *fattributes = [[NSFileManager defaultManager] attributesOfFileSystemForPath:NSHomeDirectory() error:nil];
    return [fattributes objectForKey:NSFileSystemSize];
}

- (NSNumber *) freeDiskSpace
{
    NSDictionary *fattributes = [[NSFileManager defaultManager] attributesOfFileSystemForPath:NSHomeDirectory() error:nil];
    return [fattributes objectForKey:NSFileSystemFreeSize];
}

#pragma mark platform type and name utils
- (NSUInteger) platformType
{
    NSString *platform = [self platform];

    // The ever mysterious iFPGA
    if ([platform isEqualToString:@"iFPGA"])        return UIDeviceIFPGA;

    // iPhone
    if ([platform isEqualToString:@"iPhone1,1"])    return UIDevice1GiPhone;
    if ([platform isEqualToString:@"iPhone1,2"])    return UIDevice3GiPhone;
    if ([platform hasPrefix:@"iPhone2"])            return UIDevice3GSiPhone;
    if ([platform hasPrefix:@"iPhone3"])            return UIDevice4iPhone;
    if ([platform hasPrefix:@"iPhone4"])            return UIDevice4SiPhone;
    if ([platform hasPrefix:@"iPhone5"])            return UIDevice5iPhone;
    
    // iPod
    if ([platform hasPrefix:@"iPod1"])              return UIDevice1GiPod;
    if ([platform hasPrefix:@"iPod2"])              return UIDevice2GiPod;
    if ([platform hasPrefix:@"iPod3"])              return UIDevice3GiPod;
    if ([platform hasPrefix:@"iPod4"])              return UIDevice4GiPod;

    // iPad
    if ([platform hasPrefix:@"iPad1"])              return UIDevice1GiPad;
    if ([platform hasPrefix:@"iPad2"])              return UIDevice2GiPad;
    if ([platform hasPrefix:@"iPad3"])              return UIDevice3GiPad;
    if ([platform hasPrefix:@"iPad4"])              return UIDevice4GiPad;
    
    // Apple TV
    if ([platform hasPrefix:@"AppleTV2"])           return UIDeviceAppleTV2;
    if ([platform hasPrefix:@"AppleTV3"])           return UIDeviceAppleTV3;

    if ([platform hasPrefix:@"iPhone"])             return UIDeviceUnknowniPhone;
    if ([platform hasPrefix:@"iPod"])               return UIDeviceUnknowniPod;
    if ([platform hasPrefix:@"iPad"])               return UIDeviceUnknowniPad;
    if ([platform hasPrefix:@"AppleTV"])            return UIDeviceUnknownAppleTV;
    
    // Simulator thanks Jordan Breeding
    if ([platform hasSuffix:@"86"] || [platform isEqual:@"x86_64"])
    {
        BOOL smallerScreen = [[UIScreen mainScreen] bounds].size.width < 768;
        return smallerScreen ? UIDeviceSimulatoriPhone : UIDeviceSimulatoriPad;
    }

    return UIDeviceUnknown;
}

- (NSString *) platformString
{
    switch ([self platformType])
    {
        case UIDevice1GiPhone: return IPHONE_1G_NAMESTRING;
        case UIDevice3GiPhone: return IPHONE_3G_NAMESTRING;
        case UIDevice3GSiPhone: return IPHONE_3GS_NAMESTRING;
        case UIDevice4iPhone: return IPHONE_4_NAMESTRING;
        case UIDevice4SiPhone: return IPHONE_4S_NAMESTRING;
        case UIDevice5iPhone: return IPHONE_5_NAMESTRING;
        case UIDeviceUnknowniPhone: return IPHONE_UNKNOWN_NAMESTRING;
        
        case UIDevice1GiPod: return IPOD_1G_NAMESTRING;
        case UIDevice2GiPod: return IPOD_2G_NAMESTRING;
        case UIDevice3GiPod: return IPOD_3G_NAMESTRING;
        case UIDevice4GiPod: return IPOD_4G_NAMESTRING;
        case UIDeviceUnknowniPod: return IPOD_UNKNOWN_NAMESTRING;
            
        case UIDevice1GiPad : return IPAD_1G_NAMESTRING;
        case UIDevice2GiPad : return IPAD_2G_NAMESTRING;
        case UIDevice3GiPad : return IPAD_3G_NAMESTRING;
        case UIDevice4GiPad : return IPAD_4G_NAMESTRING;
        case UIDeviceUnknowniPad : return IPAD_UNKNOWN_NAMESTRING;
            
        case UIDeviceAppleTV2 : return APPLETV_2G_NAMESTRING;
        case UIDeviceAppleTV3 : return APPLETV_3G_NAMESTRING;
        case UIDeviceAppleTV4 : return APPLETV_4G_NAMESTRING;
        case UIDeviceUnknownAppleTV: return APPLETV_UNKNOWN_NAMESTRING;
            
        case UIDeviceSimulator: return SIMULATOR_NAMESTRING;
        case UIDeviceSimulatoriPhone: return SIMULATOR_IPHONE_NAMESTRING;
        case UIDeviceSimulatoriPad: return SIMULATOR_IPAD_NAMESTRING;
        case UIDeviceSimulatorAppleTV: return SIMULATOR_APPLETV_NAMESTRING;
            
        case UIDeviceIFPGA: return IFPGA_NAMESTRING;
            
        default: return IOS_FAMILY_UNKNOWN_DEVICE;
    }
}

- (BOOL) hasRetinaDisplay
{
    return ([UIScreen mainScreen].scale == 2.0f);
}

- (UIDeviceFamily) deviceFamily
{
    NSString *platform = [self platform];
    if ([platform hasPrefix:@"iPhone"]) return UIDeviceFamilyiPhone;
    if ([platform hasPrefix:@"iPod"]) return UIDeviceFamilyiPod;
    if ([platform hasPrefix:@"iPad"]) return UIDeviceFamilyiPad;
    if ([platform hasPrefix:@"AppleTV"]) return UIDeviceFamilyAppleTV;
    
    return UIDeviceFamilyUnknown;
}

#pragma mark MAC addy
// Return the local MAC addy
// Courtesy of FreeBSD hackers email list
// Accidentally munged during previous update. Fixed thanks to mlamb.
- (NSString *) macaddress
{
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
        printf("Error: Memory allocation error\n");
        return NULL;
    }
    
    if (sysctl(mib, 6, buf, &len, NULL, 0) < 0) {
        printf("Error: sysctl, take 2\n");
        free(buf); // Thanks, Remy "Psy" Demerest
        return NULL;
    }
    
    ifm = (struct if_msghdr *)buf;
    sdl = (struct sockaddr_dl *)(ifm + 1);
    ptr = (unsigned char *)LLADDR(sdl);
    NSString *outstring = [NSString stringWithFormat:@"%02X:%02X:%02X:%02X:%02X:%02X", *ptr, *(ptr+1), *(ptr+2), *(ptr+3), *(ptr+4), *(ptr+5)];

    free(buf);
    return outstring;
}

// Illicit Bluetooth check -- cannot be used in App Store
/* 
Class  btclass = NSClassFromString(@"GKBluetoothSupport");
if ([btclass respondsToSelector:@selector(bluetoothStatus)])
{
    printf("BTStatus %d\n", ((int)[btclass performSelector:@selector(bluetoothStatus)] & 1) != 0);
    bluetooth = ((int)[btclass performSelector:@selector(bluetoothStatus)] & 1) != 0;
    printf("Bluetooth %s enabled\n", bluetooth ? "is" : "isn't");
}
*/
@end

```



#### [aso 与设备信息相关的修改](https://github.com/zhangkn/knaso/tree/master/ASO/ChangeCode)



> * [ChangeCode.m](https://github.com/zhangkn/knaso/blob/master/ASO/ChangeCode/ChangeCode.m)
>
>   ```objc
>   Skip to content
>    
>   Search or jump to…
>   
>   Pull requests
>   Issues
>   Marketplace
>   Explore
>    @zhangkn Sign out
>   1
>   1 0 zhangkn/knaso
>    Code  Issues 0  Pull requests 0  Projects 0  Wiki  Insights  Settings
>   knaso/ASO/ChangeCode/ChangeCode.m
>   4bf7055  on Jun 9
>    System Administrator 更改项目结构，使其可用化
>        
>   1715 lines (1489 sloc)  57.9 KB
>   //
>   // Created by admin on 2017/12/7.
>   //
>   
>   #import "ChangeCode.h"
>   #import "../GeneralUtil.h"
>   #import <substrate.h>
>   #import <IOKit/IOKit.h>
>   #import <SystemConfiguration/CaptiveNetwork.h>
>   #import <SystemConfiguration/SCNetworkReachability.h>
>   #import "CoreTelephony.h"
>   #import <sys/utsname.h>
>   #import <UIKit/UIKit.h>
>   #import <sys/sysctl.h>
>   #import <AdSupport/AdSupport.h>
>   #import <Security/Security.h>
>   #import <CoreLocation/CoreLocation.h>
>   #include <sys/sysctl.h>
>   #include <net/if.h>
>   #include <net/if_dl.h>
>   #import "../ProcessMsgsnd.h"
>   #import "capstone.h"
>   #import "UIDevice+Extensions.h"
>   #import "../Constant.h"
>   
>   
>   char* FakeTime = NULL;
>   
>   
>   
>   
>   @implementation ChangeCode {
>   
>   }
>   
>   NSString *NG_Product = nil;
>   // 所处国家
>   NSString *NG_isoCountryCode = nil;
>   
>   NSString *NG_SSID = nil;
>   
>   NSString *NG_BSSID = nil;
>   //序列号
>   NSString *NG_searnalNumber = nil;
>   //MAC地址
>   NSString *NG_macAdress =nil;
>   //IDFA
>   NSString *NG_IDFA =nil;
>   //IDFV
>   NSString *NG_IDFV =nil;
>   //IMEI
>   NSString *NG_IMEI =nil;
>   //UDID
>   NSString *NG_UDID =nil;
>   //设备名
>   NSString *NG_deviceName =nil;
>   //运营商
>   NSString *NG_carrierOperator =nil;
>   //系统版本
>   NSString *NG_systemVersion =nil;
>   //手机种类
>   NSString *NG_phoneType =nil;
>   //手机国家码
>   NSString *NG_mobileCountryCode = nil;  // 默认  460
>   //手机网络码
>   NSString *NG_mobileNetworkCode =nil;   //默认 11
>   //SSIDDATA
>   NSString *NG_SSIDDATA =nil;
>   //蓝牙码
>   NSString *NG_BLUETOOTH =nil;
>   // 设备型号
>   NSString *NG_UNITTYPE =nil;
>   // 设备地域
>   NSString *NG_DEVICE_LOCATION =nil;
>   // 设备名
>   NSString *NG_DEVICE_NAME =nil;
>   // 设备单独地域
>   NSString *NG_DEVICE_SINGLENAME =nil;
>   // 设备硬件型号 HardwarePlatform
>   NSString *NG_DEVICE_HardwarePlatform =nil;
>   // 设备硬件模型
>   NSString *NG_DEVICE_HardwareModel =nil;
>   // 设备生产版本 ProductType
>   NSString *NG_DEVICE_ProductType =nil;
>   // ecid  处理器型号
>   NSString *NG_DEVICE_ecidData =nil;
>   
>   // 进行获取当前的设备内容
>   NSString *NG_systemVersionWithNumber = nil;
>   NSString *NG_systemVersionWithNumber_two =nil;
>   NSString *NG_systemVersionWithEnglist = nil; // ng_vc
>   
>   
>   
>   
>   
>   
>   
>   
>   + (ChangeCode *)instance {
>       static ChangeCode *_instance = nil;
>   
>       @synchronized (self) {
>           if (_instance == nil) {
>               _instance = [[self alloc] init];
>           }
>       }
>   
>       return _instance;
>   }
>   
>   /**
>    *
>    * 进行改码一一对应的写入
>    * */
>   - (void)ng_changeCodeWithParam:(NSString *)NG_SSID
>                        withBSSID:(NSString *)NG_BSSID
>                         withIMEI:(NSString *)NG_IMEI
>                         withIDFV:(NSString *)NG_IDFV
>                         withIDFA:(NSString *)NG_IDFA
>                      withSSIDDTA:(NSString *)NG_SSIDDATA
>                    withMACadress:(NSString *)NG_MAC
>                withSearnalNumber:(NSString *)NG_SEARNALNUMBER
>                    withPhotoType:(NSString *)NG_PHOTO_TYPE
>                withSystemVersion:(NSString *)NG_systemVersion
>            withMobileCountryCode:(NSString *)NG_countryCode
>                      withProduct:(NSString *)NG_Product
>              withCarrierOperator:(NSString *)NG_carrierOperator
>                    withBlueTooch:(NSString *)NG_BLUETOOCH
>                  withDeviceModel:(NSString *)NG_DEVICEMODEL
>                         withUUID:(NSString *)NG_UUID
>   
>                       {
>   
>       //将对应的数据传递到domain中 进行保存操作
>       NSDictionary * dic1 = [[NSUserDefaults standardUserDefaults]persistentDomainForName:NSGlobalDomain];
>       NSMutableDictionary * temDic = [NSMutableDictionary dictionaryWithDictionary:dic1];
>       // 进行遍历给对应的字典
>        if(NG_MAC){
>            [temDic setObject:NG_MAC forKey:@"NG_MAC_DATA"];
>        }else{
>            [temDic setObject:@"" forKey:@"NG_MAC_DATA"];
>        }
>        if(NG_BLUETOOCH){
>            [temDic setObject:NG_BLUETOOCH forKey:@"NG_BLUETOOCH_DATA"];
>        }else {
>            [temDic setObject:@"" forKey:@"NG_BLUETOOCH_DATA"];
>        }
>        if(NG_SSID){
>            [temDic setObject:NG_SSID forKey:@"NG_SSID_DATA"];
>        }else {
>            [temDic setObject:@"" forKey:@"NG_SSID_DATA"];
>        }
>       if(NG_BSSID){
>           [temDic setObject:NG_BSSID forKey:@"NG_BSSID_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_BSSID_DATA"];
>       } if(NG_IDFV){
>           [temDic setObject:NG_IDFV forKey:@"NG_IDFV_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_IDFV_DATA"];
>       } if(NG_IMEI){
>           [temDic setObject:NG_IMEI forKey:@"NG_IMEI_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_IMEI_DATA"];
>       } if(NG_IDFA){
>           [temDic setObject:NG_IDFA forKey:@"NG_IDFA_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_IDFA_DATA"];
>       }if(NG_SSIDDATA){
>           [temDic setObject:NG_SSIDDATA forKey:@"NG_SSIDDATA_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_SSIDDATA_DATA"];
>       }if(NG_SEARNALNUMBER){
>           [temDic setObject:NG_SEARNALNUMBER forKey:@"NG_SEARNALNUMBER_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_SEARNALNUMBER_DATA"];
>       }if(NG_PHOTO_TYPE){
>           [temDic setObject:NG_PHOTO_TYPE forKey:@"NG_PHOTO_TYPE_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_PHOTO_TYPE_DATA"];
>       }if(NG_systemVersion){
>           [temDic setObject:NG_systemVersion forKey:@"NG_systemVersion_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_systemVersion_DATA"];
>       }if(NG_countryCode){
>           [temDic setObject:NG_countryCode forKey:@"NG_countryCode_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_countryCode_DATA"];
>       }if(NG_Product){
>           [temDic setObject:NG_countryCode forKey:@"NG_Product_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_Product_DATA"];
>       }if(NG_carrierOperator){
>           [temDic setObject:NG_carrierOperator forKey:@"NG_carrierOperator_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_carrierOperator_DATA"];
>       }if(NG_DEVICEMODEL){
>           [temDic setObject:NG_DEVICEMODEL forKey:@"NG_DEVICETYPE_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_DEVICETYPE_DATA"];
>       }if(NG_UUID){
>           [temDic setObject:NG_UUID forKey:@"NG_UUID_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_UUID_DATA"];
>       }
>        [ProcessMsgsnd sendMessage:temDic];
>   }
>   
>   
>   
>   -(void)ng_changeCodeWithParam:(NSString *)NG_BLUADRESS
>   with_NG_BuildVersion:(NSString *)NG_BIILD_VERSION
>           with_NG_ecid:(NSString *)NG_ECID
>           with_NG_hardwareModel:(NSString *)NG_HARDWARE_MODEL
>           with_NG_hardwarePlatform:(NSString *)NG_HAEDWARE_PLATFORM
>           with_NG_idfa:(NSString *)NG_IDFA
>           with_NG_imei:(NSString *)NG_IMEI
>           with_NG_productType:(NSString *)NG_PEODUCt_TYPE
>           with_NG_productVersion:(NSString *)NG_PEODUCt_VERSION
>           with_NG_serialNumber:(NSString *)NG_SERIALNUMBER
>           with_NG_UUID:(NSString *)NG_UUID
>           with_NG_wifiAddress:(NSString *)NG_WIFIADRESS{
>       //将对应的数据传递到domain中 进行保存操作
>       NSDictionary * dic1 = [[NSUserDefaults standardUserDefaults]persistentDomainForName:NSGlobalDomain];
>       NSMutableDictionary * temDic = [NSMutableDictionary dictionaryWithDictionary:dic1];
>       // 进行遍历给对应的字典
>       if(NG_WIFIADRESS){
>           [temDic setObject:NG_WIFIADRESS forKey:@"NG_MAC_DATA"];
>       }else{
>           [temDic setObject:@"" forKey:@"NG_MAC_DATA"];
>       }
>       if(NG_BLUADRESS){
>           [temDic setObject:NG_BLUADRESS forKey:@"NG_BLUETOOCH_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_BLUETOOCH_DATA"];
>       }
>       if(NG_IMEI){
>           [temDic setObject:NG_IMEI forKey:@"NG_IMEI_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_IMEI_DATA"];
>       }
>       if(NG_IDFA){
>           [temDic setObject:NG_IDFA forKey:@"NG_IDFA_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_IDFA_DATA"];
>       }
>       if(NG_SERIALNUMBER){
>           [temDic setObject:NG_SERIALNUMBER forKey:@"NG_SEARNALNUMBER_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_SEARNALNUMBER_DATA"];
>       }
>       if(NG_PEODUCt_VERSION){
>           [temDic setObject:NG_PEODUCt_VERSION forKey:@"NG_systemVersion_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_systemVersion_DATA"];
>       }
>   
>       if(NG_BIILD_VERSION){
>           [temDic setObject:NG_BIILD_VERSION forKey:@"NG_BUILD_VERSION"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_BUILD_VERSION"];
>       }
>   
>       if(NG_PEODUCt_TYPE){
>           [temDic setObject:NG_PEODUCt_TYPE forKey:@"NG_PRODUCT_TYPE_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_PRODUCT_TYPE_DATA"];
>       }
>       if(NG_HAEDWARE_PLATFORM){
>           [temDic setObject:NG_HAEDWARE_PLATFORM forKey:@"NG_HARDWARE_PATFORM_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_HARDWARE_PATFORM_DATA"];
>       }
>       if(NG_HARDWARE_MODEL){
>           [temDic setObject:NG_HARDWARE_MODEL forKey:@"NG_HARDWARE_MODEL_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_HARDWARE_MODEL_DATA"];
>       }
>       if(NG_ECID){
>           [temDic setObject:NG_ECID forKey:@"NG_ECID_DATA"];
>       }else {
>           [temDic setObject:@"" forKey:@"NG_ECID_DATA"];
>       }
>       [ProcessMsgsnd sendMessage:temDic];
>   
>   }
>   
>   
>   /**
>    * 将domain中存储的参数，设置为当前需要改码的内容中
>    *
>    * */
>   - (void)ng_getDomainUserSettingsParams {
>       id dic                          = [ProcessMsgsnd getMessage];
>       if(!dic){
>           return;
>       }
>       NSString *SSID                  = [dic objectForKeyedSubscript:@"NG_SSID_DATA"];
>       NSString *BSSID                 = [dic objectForKeyedSubscript:@"NG_BSSID_DATA"];
>       NSString *IMEI                  = [dic objectForKeyedSubscript:@"NG_IMEI_DATA"];
>       NSString *IDFV                  = [dic objectForKeyedSubscript:@"NG_IDFV_DATA"];
>       NSString *IDFA                  = [dic objectForKeyedSubscript:@"NG_IDFA_DATA"];
>       NSString *SSIDDATA              = [dic objectForKeyedSubscript:@"NG_SSIDDATA_DATA"];
>       NSString *MAC                   = [dic objectForKeyedSubscript:@"NG_MAC_DATA"];
>       NSString *SEARNALNUMBER         = [dic objectForKeyedSubscript:@"NG_SEARNALNUMBER_DATA"];
>       NSString *PHOTO_TYPE            = [dic objectForKeyedSubscript:@"NG_PHOTO_TYPE_DATA"];
>       NSString *systemVersion         = [dic objectForKeyedSubscript:@"NG_systemVersion_DATA"];
>       NSString *countryCode           = [dic objectForKeyedSubscript:@"NG_countryCode_DATA"];
>       NSString *Product               = [dic objectForKeyedSubscript:@"NG_Product_DATA"];
>       NSString *carrierOperator       = [dic objectForKeyedSubscript:@"NG_carrierOperator_DATA"];
>       NSString *BLUETOOCH             = [dic objectForKeyedSubscript:@"NG_BLUETOOCH_DATA"];
>       NSString *DeviceType            = [dic objectForKeyedSubscript:@"NG_DEVICETYPE_DATA"];
>       NSString *UUID                  = [dic objectForKeyedSubscript:@"NG_UUID_DATA"];
>       NSString *buildVersion          = [dic objectForKeyedSubscript:@"NG_BUILD_VERSION"];
>       NSString *productType           = [dic objectForKeyedSubscript:@"NG_PRODUCT_TYPE_DATA"];
>       NSString *hardwarePatform       = [dic objectForKeyedSubscript:@"NG_HARDWARE_PATFORM_DATA"];
>       NSString *hardwareModel         = [dic objectForKeyedSubscript:@"NG_HARDWARE_MODEL_DATA"];
>       NSString *ecidData              = [dic objectForKeyedSubscript:@"NG_ECID_DATA"];
>   
>   
>       if(![GeneralUtil isBlankString:buildVersion]){
>           NG_systemVersionWithEnglist = buildVersion;
>       }
>       if(![GeneralUtil isBlankString:productType]){
>           NG_DEVICE_ProductType = productType;
>       }
>       if(![GeneralUtil isBlankString:hardwarePatform]){
>           NG_DEVICE_HardwarePlatform = hardwarePatform;
>       }
>       if(![GeneralUtil isBlankString:hardwareModel]){
>           NG_DEVICE_HardwareModel = hardwareModel;
>       }
>       if(![GeneralUtil isBlankString:ecidData]){
>           NG_DEVICE_ecidData = ecidData;
>       }
>       if([GeneralUtil isBlankString:UUID]){
>           NG_UDID =  [dic objectForKeyedSubscript:@"ng_UUID"];
>       }else{
>           NG_UDID = UUID;
>       }
>       //[GeneralUtil isBlankString:DeviceType]
>       if([GeneralUtil isBlankString:DeviceType]){
>           NG_UNITTYPE =  [dic objectForKeyedSubscript:@"ng_numberModel"];
>       }else{
>           NG_UNITTYPE = nil;
>       }
>       if([GeneralUtil isBlankString:BLUETOOCH]){
>           NG_BLUETOOTH =  [dic objectForKeyedSubscript:@"ng_BluetoothAddress"];
>       }else{
>           NG_BLUETOOTH = BLUETOOCH;
>       }
>       if([GeneralUtil isBlankString:SSID]){
>           // 如果未null的话 则将原机型替换
>           NG_SSID =  [dic objectForKeyedSubscript:@"ng_SSID"];
>       }else{
>           NG_SSID  = SSID;
>       }
>       if([GeneralUtil isBlankString:BSSID]){
>           // 如果未null的话 则将原机型替换
>           NG_BSSID =  [dic objectForKeyedSubscript:@"ng_BSSID"];
>       }else{
>           NG_BSSID  = BSSID;
>       }
>       if([GeneralUtil isBlankString:IMEI]){
>           // 如果未null的话 则将原机型替换
>           NG_IMEI =  [dic objectForKeyedSubscript:@"ng_Imei"];
>       }else{
>           NG_IMEI  = IMEI;
>       }
>       if([GeneralUtil isBlankString:IDFV]){
>           // 如果未null的话 则将原机型替换
>           NG_IDFV =  [dic objectForKeyedSubscript:@"ng_identifierForVendor"];
>       }else{
>           NG_IDFV  = IDFV;
>       }
>       if([GeneralUtil isBlankString:SSIDDATA]){
>           // 如果未null的话 则将原机型替换
>           NG_SSIDDATA =  [dic objectForKeyedSubscript:@"ng_SSIDDATA"];
>       }else{
>           NG_SSIDDATA  = SSIDDATA;
>       }
>       if([GeneralUtil isBlankString:IDFA]){
>           // 如果未null的话 则将原机型替换
>           NG_IDFA =  [dic objectForKeyedSubscript:@"ng_advertisingIdentifier"];
>       }else{
>           NG_IDFA  = IDFA;
>       }if([GeneralUtil isBlankString:MAC]){
>           // 如果未null的话 则将原机型替换
>           NG_macAdress =  [dic objectForKeyedSubscript:@"ng_macaddress"];
>       }else{
>           NG_macAdress  = MAC;
>       }
>       if([GeneralUtil isBlankString:SEARNALNUMBER]){
>           // 如果未null的话 则将原机型替换
>           NG_searnalNumber =  [dic objectForKeyedSubscript:@"ng_ServeralNumber"];
>       }else{
>           NG_searnalNumber  = SEARNALNUMBER;
>       }
>       if([GeneralUtil isBlankString:PHOTO_TYPE]){
>           // 如果未null的话 则将原机型替换
>           NG_phoneType =  [dic objectForKeyedSubscript:@"ng_advertisingIdentifier"];
>       }else{
>           NG_phoneType  = PHOTO_TYPE;
>       }
>       if([GeneralUtil isBlankString:systemVersion]){
>           // 如果未null的话 则将原机型替换
>           NG_systemVersion =  [dic objectForKeyedSubscript:@"ng_systemVersion"];
>       }else{
>           NG_systemVersion  = systemVersion;
>       }
>       if([GeneralUtil isBlankString:countryCode]){
>           // 如果未null的话 则将原机型替换
>           NG_isoCountryCode =  [dic objectForKeyedSubscript:@"ng_isoCountryCode"];
>       }else{
>           NG_isoCountryCode  = countryCode;
>       }
>   
>       if([GeneralUtil isBlankString:Product]){
>           // 如果未null的话 则将原机型替换
>           NG_Product =  [dic objectForKeyedSubscript:@"ng_model"];
>       }else{
>           NG_Product  = Product;
>       }
>       if([GeneralUtil isBlankString:carrierOperator]){
>           // 如果未null的话 则将原机型替换
>           NG_carrierOperator =  [dic objectForKeyedSubscript:@"ng_carrierName"];
>       }else{
>           NG_carrierOperator  = carrierOperator;
>       }
>   
>   }
>   
>   /**
>    *
>    * 保存设备原始码
>    * */
>   - (void)ng_saveOrignDeviceInfo {
>       // 保存前置空  避免内存中存在上次改码后的设备信息
>       NG_Product = nil;
>       NG_isoCountryCode = nil;
>       NG_SSID = nil;
>       NG_BSSID = nil;
>       NG_searnalNumber = nil;
>       NG_macAdress =nil;
>       NG_IDFA =nil;
>       NG_IDFV =nil;
>       NG_IMEI =nil;
>       NG_UDID =nil;
>       NG_deviceName =nil;
>       NG_carrierOperator =nil;
>       NG_systemVersion =nil;
>       NG_phoneType =nil;
>       NG_mobileCountryCode = nil;  // 默认  460
>       NG_mobileNetworkCode =nil;   //默认 11
>       NG_SSIDDATA =nil;
>       NG_BLUETOOTH =nil;
>       NG_UNITTYPE =nil;
>       NG_systemVersionWithNumber = nil;
>       NG_systemVersionWithNumber_two =nil;
>       NG_systemVersionWithEnglist = nil; // ng_vc
>       // 进行保存初始化的数据
>       UIDevice* device                                = [UIDevice currentDevice];
>       NSString * model                                = [device model];
>       NSString * localizedModel                       = [device localizedModel];
>       NSString * systemVersion                        = [device systemVersion];
>       NSString * name                                 = [device name];
>       NSString * identifierForVendor                  = [[device identifierForVendor] UUIDString];
>       NSString * advertisingIdentifier                = [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];
>       CTTelephonyNetworkInfo * ctTelephonyNetworkInfo = [[[CTTelephonyNetworkInfo alloc] init] autorelease];
>       CTCarrier* ctCarrier                            = [ctTelephonyNetworkInfo subscriberCellularProvider];
>       NSString * carrierName                          = [ctCarrier carrierName];
>       NSString * mobileCountryCode                    = [ctCarrier mobileCountryCode]?[ctCarrier mobileCountryCode]:@"";
>       NSString * mobileNetworkCode                    = [ctCarrier mobileNetworkCode]?[ctCarrier mobileNetworkCode]:@"";
>       NSString * isoCountryCode                       = [ctCarrier isoCountryCode]?[ctCarrier isoCountryCode]:@"";
>       NSString * currentRadioAccessTechnology         = [ctTelephonyNetworkInfo currentRadioAccessTechnology]?[ctTelephonyNetworkInfo currentRadioAccessTechnology]:@"";
>       NSString * macaddress                           = [self macaddress];
>       NSString * ServeralNumber                       = [GeneralUtil getServeral];
>       NSString * UUID                                 = [GeneralUtil getUUID];
>       NSString *BSSID = nil;
>       NSString *SSID = nil;
>       NSString *SSIDDATA = nil;
>       // 获取BlueTooch 地址
>       void *gestalt = dlopen("/usr/lib/libMobileGestalt.dylib", RTLD_GLOBAL | RTLD_LAZY);
>       CFStringRef (*MGCopyAnswer)(CFStringRef) = (CFStringRef (*)(CFStringRef))(dlsym(gestalt, "MGCopyAnswer"));
>       id blAddress                        =   CFBridgingRelease( MGCopyAnswer(CFSTR("BluetoothAddress")));
>       id MN                               =   CFBridgingRelease( MGCopyAnswer(CFSTR("ModelNumber")));
>       id RIF                              =   CFBridgingRelease( MGCopyAnswer(CFSTR("RegionInfo")));
>       id iMEI                             =   CFBridgingRelease( MGCopyAnswer(CFSTR("InternationalMobileEquipmentIdentity")));
>       NSString *BluetoothAddress          =   blAddress?blAddress:@"";
>       NSString *ModelNumber               =   MN?MN:@"";
>       NSString *RegionInfo                =   RIF?RIF:@"";
>       // IMEI
>       NSString *IMEI                      =   iMEI?iMEI:@"";
>       // 进行获取当前的设备内容
>       NSString *numberModel               =   [ModelNumber stringByAppendingString:RegionInfo];
>   
>       NSArray *ifs = (__bridge  id)CNCopySupportedInterfaces();
>   
>       for (NSString *ifname in ifs) {
>           NSDictionary *info = (__bridge id)CNCopyCurrentNetworkInfo((__bridge CFStringRef)ifname);
>           if (info[@"BSSID"])
>           {
>               BSSID = info[@"BSSID"];
>   
>           }
>           if (info[@"SSID"])
>           {
>               SSID = info[@"SSID"];
>   
>           }
>           if (info[@"SSIDDATA"])
>           {
>   //            SSIDDATA = info[@"SSIDDATA"];
>               // 需要转化为NSData
>               SSIDDATA = [[[NSString alloc] initWithData:info[@"SSIDDATA"] encoding:NSUTF8StringEncoding] autorelease];
>   
>           }
>       }
>   //    NSLog(@"-------model = %@\n"
>   //            "-------localizedModel = %@\n"
>   //            "-------systemVersion = %@\n"
>   //            "-------name = %@\n"
>   //            "-------identifierForVendor = %@\n"
>   //            "-------advertisingIdentifier = %@\n"
>   //            "-------carrierName = %@\n"
>   //            "-------mobileCountryCode = %@\n"
>   //            "-------mobileNetworkCode = %@\n"
>   //            "-------isoCountryCode = %@\n"
>   //            "-------currentRadioAccessTechnology = %@\n"
>   //            "-------BSSID = %@\n"
>   //            "-------SSID = %@\n"
>   //            "-------SSIDDATA = %@\n"
>   //            "-------ServeralNumber = %@\n"
>   //            "-------UUID = %@\n"
>   //            "-------macaddress = %@\n"
>   //
>   //                    ,
>   //            model,localizedModel,systemVersion,name,identifierForVendor
>   //            ,advertisingIdentifier,carrierName,mobileCountryCode
>   //            ,mobileNetworkCode,isoCountryCode,currentRadioAccessTechnology,BSSID,SSID,SSIDDATA,ServeralNumber,UUID,macaddress);
>       // 进行保存
>       NSString *jsonString = nil;
>       NSString *docPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
>       NSString *fileDicPath = [docPath stringByAppendingPathComponent:[GeneralUtil convertHexStrToString:NG_SAVE_FileName]];
>       NSDictionary *dic = @{
>               @"ng_model":model,
>               @"ng_localizedModel":localizedModel,
>               @"ng_systemVersion":systemVersion,
>               @"ng_name":name,
>               @"ng_identifierForVendor":identifierForVendor,
>               @"ng_advertisingIdentifier":advertisingIdentifier,
>               @"ng_carrierName":carrierName,
>               @"ng_mobileCountryCode":mobileCountryCode,
>               @"ng_mobileNetworkCode":mobileNetworkCode,
>               @"ng_isoCountryCode":isoCountryCode,
>               @"ng_currentRadioAccessTechnology":currentRadioAccessTechnology,
>               @"ng_macaddress":macaddress,
>               @"ng_ServeralNumber":ServeralNumber,
>               @"ng_UUID":UUID,
>               @"ng_SSID":SSID,
>               @"ng_SSIDDATA":SSIDDATA,
>               @"ng_BSSID":BSSID,
>               @"ng_BluetoothAddress":BluetoothAddress,
>               @"ng_Imei":IMEI,
>               @"ng_numberModel":numberModel,
>       };
>   
>   
>       NSError *error;
>       NSData * jsonData = [NSJSONSerialization dataWithJSONObject:dic options:NSJSONWritingPrettyPrinted error:&error];
>       if(!jsonData){
>           NSLog(@"----------ERROR------");
>       } else{
>           jsonString = [[[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding] autorelease];
>       }
>       //将其转化为加密后的字符串
>       NSString *aesString = [GeneralUtil AES128Encryption:[GeneralUtil convertHexStrToString:NG_SAVE_KEY] withContent:jsonString];
>       // 进行请求 增加设备接口
>       NSError *errorFile;
>       // 将加密字符写入时执行的方法
>      [aesString writeToFile:fileDicPath atomically:YES encoding:NSUTF8StringEncoding error:&errorFile];
>   }
>   
>   /**
>    *
>    * 从domain中获取原始码
>    * */
>   - (BOOL)ng_getOrignDeviceInfoFromSavedToDomain {
>   
>       NSString *docPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
>       NSString *fileDicPath = [docPath stringByAppendingPathComponent:[GeneralUtil convertHexStrToString:NG_SAVE_FileName]];
>       // 进行读取文件路径
>       NSError *errorFile;
>       NSString * fileContent = [NSString stringWithContentsOfFile:fileDicPath encoding:NSUTF8StringEncoding error:&errorFile];
>       if(!fileContent){
>           return FALSE;
>       }
>       NSString * jsonString = [GeneralUtil AES128Deciphering:[GeneralUtil convertHexStrToString:NG_SAVE_KEY] withContent:fileContent];
>       if(!jsonString){
>           return FALSE;
>       }
>   
>       NSError *error = nil;
>       NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:[jsonString dataUsingEncoding:NSUTF8StringEncoding] options:NSJSONReadingMutableContainers error:&error];
>       if(!dic){
>           return FALSE;
>       }
>       //将对应的数据传递到domain中 进行保存操作
>       NSDictionary * dic1 = [[NSUserDefaults standardUserDefaults]persistentDomainForName:NSGlobalDomain];
>   //    NSDictionary * temDic = [NSDictionary dictionaryWithDictionary:dic1];
>       NSMutableDictionary * temDic = [NSMutableDictionary dictionaryWithDictionary:dic1];
>   
>       // 进行遍历给对应的字典
>       [dic enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
>           [temDic setObject:obj?obj:@"" forKey:key];
>       }];
>       [ProcessMsgsnd sendMessage:temDic];
>       return TRUE;
>   }
>   
>   
>   - (NSDictionary *)ng_getOrignDataSets {
>   
>       NSError *errorFile;
>       NSString * fileContent = [NSString stringWithContentsOfFile:[GeneralUtil convertHexStrToString:NG_SAVE_File] encoding:NSUTF8StringEncoding error:&errorFile];
>       if(!fileContent){
>           return nil;
>       }
>       NSString * jsonString = [GeneralUtil AES128Deciphering:[GeneralUtil convertHexStrToString:NG_SAVE_KEY] withContent:fileContent];
>       if(!jsonString){
>           return nil;
>       }
>   
>       NSError *error = nil;
>       NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:[jsonString dataUsingEncoding:NSUTF8StringEncoding] options:NSJSONReadingMutableContainers error:&error];
>       if(!dic){
>           return nil;
>       }
>       return dic;
>   }
>   
>   
>   - (NSString *) macaddress
>   {
>   
>       int         mib[6];
>       size_t       len;
>       char        *buf;
>       unsigned char    *ptr;
>       struct if_msghdr  *ifm;
>       struct sockaddr_dl *sdl;
>   
>       mib[0] = CTL_NET;
>       mib[1] = AF_ROUTE;
>       mib[2] = 0;
>       mib[3] = AF_LINK;
>       mib[4] = NET_RT_IFLIST;
>   
>       if ((mib[5] = if_nametoindex("en0")) == 0) {
>           printf("Error: if_nametoindex error/n");
>           return NULL;
>       }
>   
>       if (sysctl(mib, 6, NULL, &len, NULL, 0) < 0) {
>           printf("Error: sysctl, take 1/n");
>           return NULL;
>       }
>   
>       if ((buf = malloc(len)) == NULL) {
>           printf("Could not allocate memory. error!/n");
>           return NULL;
>       }
>   
>       if (sysctl(mib, 6, buf, &len, NULL, 0) < 0) {
>           printf("Error: sysctl, take 2");
>           return NULL;
>       }
>       ifm = (struct if_msghdr *)buf;
>       sdl = (struct sockaddr_dl *)(ifm + 1);
>       ptr = (unsigned char *)LLADDR(sdl);
>       NSString *outstring = [NSString stringWithFormat:@"%02x:%02x:%02x:%02x:%02x:%02x", *ptr, *(ptr+1), *(ptr+2), *(ptr+3), *(ptr+4), *(ptr+5)];
>   
>       free(buf);
>   
>       return [outstring uppercaseString];
>   }
>   
>   //进行截取当前的设备名 ME335
>   -(NSString *)InterceptDeviceWithName:(NSString *)name{
>       if([GeneralUtil isBlankString:name]){
>           return nil;
>       }
>       return [name substringWithRange:NSMakeRange(0, 5) ];;
>   }
>   // 进行截取出当前的设备地域 eg: J/A
>   -(NSString *)InterceptDeviceWithLocation:(NSString *)name{
>       if([GeneralUtil isBlankString:name]){
>           return nil;
>       }
>       return [name substringWithRange:NSMakeRange(5, [name length] - 5)];
>   }
>   // 进行获取当前的额设备 地区 eg: J
>   -(NSString *)InterceptDeviceWithSingleLocation:(NSString *)name{
>       if([GeneralUtil isBlankString:name]){
>           return nil;
>       }
>       return [name substringWithRange:NSMakeRange([name length] -3 ,1)];
>   }
>   
>   
>   //// ***********************************************以下为改码内容*****************************************************************
>   
>   
>   //char* FakeTime = NULL;
>   
>   
>   CFTypeRef
>   *(*old_IORegistryEntryCreateCFProperties)(
>           io_registry_entry_t	entry,
>           CFStringRef		key,
>           CFAllocatorRef		allocator,
>           IOOptionBits		options );
>   
>   
>   CFTypeRef *new_IORegistryEntryCreateCFProperties(
>           io_registry_entry_t	entry,
>           CFStringRef		key,
>           CFAllocatorRef		allocator,
>           IOOptionBits		options ){
>       NSString *type = (__bridge NSString *)key;
>   
>   
>       if(NG_searnalNumber && [type isEqualToString:@"IOPlatformSerialNumber"]){
>   
>           return (__bridge CFTypeRef *)NG_searnalNumber;
>       }
>       if(NG_IMEI && [type isEqualToString:@"device-imei"]){
>   
>           return (__bridge CFTypeRef *)NG_IMEI;
>       }
>       return old_IORegistryEntryCreateCFProperties(entry,key,allocator,options);
>   }
>   
>   CFTypeRef
>   *(*old_IORegistryEntrySearchCFProperty)(
>           io_registry_entry_t	entry,
>           const io_name_t		plane,
>           CFStringRef		key,
>           CFAllocatorRef		allocator,
>           IOOptionBits		options );
>   CFTypeRef
>   *new_IORegistryEntrySearchCFProperty(
>           io_registry_entry_t	entry,
>           const io_name_t		plane,
>           CFStringRef		key,
>           CFAllocatorRef		allocator,
>           IOOptionBits		options ){
>   
>       NSString *type = (__bridge NSString *)key;
>   //    id ss = (__bridge id) old_IORegistryEntrySearchCFProperty(entry,plane,key,allocator,options);
>   //    NSLog(@"------------------------ <<<<new_IORegistryEntrySearchCFProperty>>>>--\n----------------<<< %@  >>>-------------\n---------------------<<<< %@ >>>>----------", type,ss);
>   //    NSLog(@"-----new_IORegistryEntrySearchCFProperty--------<<<< MAC >>>--------------- = %@", [[NSString alloc] initWithData:ss encoding:CFStringConvertEncodingToNSStringEncoding(kCFStringEncodingGB_18030_2000)]);
>   
>   
>       if(NG_searnalNumber &&[type isEqualToString:@"serial-number"]){
>   
>           return (__bridge CFTypeRef *)[NG_searnalNumber dataUsingEncoding:NSUTF8StringEncoding];
>       }
>       if(NG_IMEI &&[type isEqualToString:@"device-imei"]){
>   
>           return (__bridge CFTypeRef *)[NG_IMEI dataUsingEncoding:NSUTF8StringEncoding];
>       }
>   // product-id   nsdata类型
>   
>   // software-behavior
>   
>   
>       return old_IORegistryEntrySearchCFProperty(entry,plane,key,allocator,options);
>   }
>   
>   CFDictionaryRef __nullable
>   *(*old_CNCopyCurrentNetworkInfo)	(CFStringRef interfaceName)	__OSX_AVAILABLE_STARTING(__MAC_NA,__IPHONE_4_1);
>   
>   
>   NSString *dd =nil;
>   
>   CFDictionaryRef __nullable
>   *new_CNCopyCurrentNetworkInfo	(CFStringRef interfaceName)	__OSX_AVAILABLE_STARTING(__MAC_NA,__IPHONE_4_1){
>       // 修改SSID
>       if(NG_SSID && NG_BSSID && NG_SSIDDATA){
>           return (__bridge CFDictionaryRef *) [[@{@"SSID": NG_SSID, @"BSSID": NG_BSSID, @"SSIDDATA": [NG_SSIDDATA dataUsingEncoding:NSUTF8StringEncoding]} copy] autorelease];
>       }
>       return old_CNCopyCurrentNetworkInfo(interfaceName);
>   }
>   
>   CFArrayRef __nullable
>   *(*old_CNCopySupportedInterfaces)	(void)			__OSX_AVAILABLE_STARTING(__MAC_10_8,__IPHONE_4_1);
>   
>   
>   CFArrayRef __nullable
>   *new_CNCopySupportedInterfaces	(void)			__OSX_AVAILABLE_STARTING(__MAC_10_8,__IPHONE_4_1){
>       return old_CNCopySupportedInterfaces();
>   }
>   
>   void *(*old_CTServerConnectionCopyMobileIdentity)(struct CTResult *, struct CTServerConnection *, NSString **);
>   
>   void *new_CTServerConnectionCopyMobileIdentity(struct CTResult *result, struct CTServerConnection *connection, NSString **imi){
>        old_CTServerConnectionCopyMobileIdentity(result,connection,imi);
>       if(NG_IMEI){
>           *imi = NG_IMEI;
>           return  NG_IMEI;
>   
>       }
>   
>       return old_CTServerConnectionCopyMobileIdentity(result,connection,imi);
>   
>   }
>   
>   
>   
>   int	 *(*old_sysctlnametomib)(const char *, int *, size_t *);
>   
>   int	*new_sysctlnametomib(const char *a, int *b, size_t *c){
>       return old_sysctlnametomib(a,b,c);
>   }
>   
>   
>   
>   
>   
>   int *(*old_uname)(struct utsname *);
>   
>   int *new_uname(struct utsname *name){
>       // 进行更改相关内容
>       int* a = old_uname(name);
>   
>       if(NG_deviceName){
>           strcpy(name->nodename, [NG_deviceName UTF8String]);
>       }
>       if(NG_phoneType){
>           strcpy(name->machine, [NG_phoneType UTF8String]);
>       }
>       if(NG_systemVersionWithNumber_two){
>           NSString * version = [NSString stringWithFormat:@"Darwin Kernel Version %@: Fri Feb 19 13:54:53 PST 2016; root:xnu-3248.41.4~28/RELEASE_ARM64_S5L8960X",NG_systemVersionWithNumber_two];
>           strcpy(name->version, [version UTF8String]);
>           strcpy(name->release, [NG_systemVersionWithNumber_two UTF8String]);
>       }
>       return a;
>   
>   }
>   
>   //
>   //int	 *(*old_sysctl)(int *, u_int, void *, size_t *, void *, size_t);
>   //int	*new_sysctl(int *a, u_int b, void * c, size_t * d, void * e, size_t f){
>   //    //[[NSString alloc] initWithCString:(const char*)char_arrayencoding:NSASCIIStringEncoding]
>   ////    NSLog(@"----------------------------new_sysctl------------------------------------%@---",[NSString stringWithFormat:@"%s", (char*)c]);
>   //    const char * systemVersion;
>   //    NSString *device = nil;
>   //    if(!NG_IMEI ){
>   //        return  old_sysctl(a,b,c,d,e,f);
>   //    }
>   //
>   //    if(b != 2){
>   //        return  old_sysctl(a,b,c,d,e,f);
>   //    }
>   //    if(*a == 1){
>   //        if(a[1] == 65){
>   //            device = NG_IMEI;
>   //
>   //        }else{
>   //            if(a[1] == 2){
>   //                device = NG_IMEI;
>   //
>   //            }
>   //            else{
>   //                if(a[1] != 10){
>   //                    return  old_sysctl(a,2,c,d,e,f);
>   //                }
>   //                device = NG_IMEI;
>   //            }
>   //
>   //        }
>   //        systemVersion = [device UTF8String];
>   //        if(!c){
>   //            *d = strlen(systemVersion) +1;
>   //            return 0;
>   //        }
>   //        if(*d){
>   //            strncpy(c, systemVersion, *d);
>   //        }
>   //        return  old_sysctl(a,2,c,d,e,f);
>   //
>   //    }
>   //    if(*a != 6){
>   //        return  old_sysctl(a,2,c,d,e,f);
>   //
>   //    }
>   //    if(a[1] == 2){
>   //        systemVersion = [NG_IMEI UTF8String];
>   //    }
>   //    if(a[1] != 1){
>   //        return  old_sysctl(a,2,c,d,e,f);
>   //    }
>   //
>   //    systemVersion = [NG_IMEI UTF8String];
>   //    if(!c){
>   //        *d = strlen(systemVersion) + 1;
>   //        return 0;
>   //    }
>   //    if(*d){
>   //        strncpy(c, systemVersion, *d);
>   //        return 0;
>   //    }
>   //    return old_sysctl(a,b,c,d,e,f);
>   //}
>   
>   
>   
>   //
>   //int	 *(*old_sysctlbyname)(const char *, void *, size_t *, void *, size_t);
>   //int	*new_sysctlbyname(const char * a, void *b, size_t *c, void *d, size_t e){
>   ////    NSLog(@"------------------------------《new_sysctlbyname》-----------------------%@-- ",[[[NSString alloc] initWithUTF8String:a] autorelease]);
>   ////    NSLog(@"----------------------------《new_sysctlbyname》------------------------------------%ld---",(long )*c);
>   //    if(!NG_deviceName || !NG_systemVersionWithNumber||!NG_systemVersionWithNumber_two){
>   //        return old_sysctlbyname(a,b,c,d,e);
>   //    }
>   //
>   //    const  char * deviceType ;
>   //
>   //    if(!a){
>   //        return old_sysctlbyname(a,b,c,d,e);
>   //    }
>   //    if(!strcmp(a, "hw.machine")){
>   //        deviceType = [NG_deviceName UTF8String];
>   //        if(!b){
>   //            return 0;
>   //        }
>   //
>   //        if(!*c){
>   //
>   //            old_sysctlbyname(a,b,c,d,e);
>   //            return 0;
>   //        }
>   //        // deviceIdentifier
>   //        strncpy(b,deviceType , *c);
>   //        return 0;
>   //
>   //    }
>   //    if(!strcmp(a, "hw.model")){
>   //        // deviceInternalName
>   //        deviceType =[NG_deviceName UTF8String];
>   //        if(b){
>   //            if(!*c){
>   //                return old_sysctlbyname(a,b,c,d,e);
>   //            }
>   //            strncpy(b, deviceType, *c);
>   //            return 0;
>   //        }
>   //        *c = strlen(deviceType) + 1;
>   //        return 0;
>   //    }
>   //
>   //    if(!strcmp(a, "kern.osrelease") && NG_systemVersionWithNumber ){
>   //
>   //        deviceType = [NG_systemVersionWithNumber UTF8String];
>   //    }
>   //    int version = strcmp(a, "kern.version");
>   //    if(!version|| !NG_systemVersionWithNumber_two){
>   //        return old_sysctlbyname(a,b,c,d,e);
>   //    }
>   //
>   //    if(b) {
>   //
>   //        deviceType = [[[NSString stringWithCString:b encoding:NSUTF8StringEncoding] stringByReplacingOccurrencesOfString:NG_systemVersionWithNumber_two withString:NG_systemVersionWithNumber] UTF8String];
>   //        if (!*c) {
>   //            return old_sysctlbyname(a, b, c, d, e);
>   //        }
>   //        strncpy(b, deviceType, *c);
>   //    }
>   //    return old_sysctlbyname(a,b,c,d,e);
>   //}
>   //
>   
>   NSString* (*old_mode)();
>   
>   
>   NSString* new_mode()
>   {
>       NSString * mode =  old_mode();
>       if(NG_Product){
>           return NG_Product;
>       }
>       return mode;
>   }
>   
>   
>   NSString	*(*old_localizedModel)();
>   
>   
>   NSString *	 new_localizedModel()
>   {
>       NSString * localizedModel =   old_localizedModel();
>   
>       if(NG_Product){
>           return NG_Product;
>       }
>   
>       return localizedModel;
>   }
>   NSString	*(*old_systemVersion)();
>   
>   
>   NSString *	 new_systemVersion()
>   {
>       NSString * systemVersion =old_systemVersion();
>       if(NG_systemVersion){
>           return NG_systemVersion;
>       }
>   
>       return systemVersion;
>   }
>   
>   
>   NSString	*(*old_name)();
>   
>   NSString *	 new_name()
>   {
>       NSString * name = old_name();
>       if(NG_deviceName){
>           return NG_deviceName;
>       }
>       return name;
>   }
>   NSUUID	*(*old_identifierForVendor)();
>   
>   
>   NSUUID *	 new_identifierForVendor()
>   {
>       NSUUID * identifierForVendo = old_identifierForVendor();
>   
>       if(NG_IDFV){
>           return [[[NSUUID alloc] initWithUUIDString:NG_IDFV] autorelease];
>       }
>       return identifierForVendo;
>   }
>   NSUUID	*(*old_advertisingIdentifier)(id self, SEL _cmd);
>   
>   
>   NSUUID *	 new_advertisingIdentifier(id self, SEL _cmd)
>   {
>       NSUUID * identifierForVendo =  old_advertisingIdentifier(self,_cmd);
>   
>       if(NG_IDFA){
>           return [[[NSUUID alloc] initWithUUIDString:NG_IDFA] autorelease];
>       }
>       return identifierForVendo;
>   }
>   
>   
>   NSString	*(*old_carrierName)();
>   
>   
>   NSString *	 new_carrierName()
>   {
>       NSString *	 carrierName =  (*old_carrierName)();
>       if(NG_carrierOperator){
>           return NG_carrierOperator;
>       }
>       return carrierName;
>   }
>   
>   
>   NSString	*(*old_mobileCountryCode)();
>   
>   
>   NSString *	 new_mobileCountryCode()
>   {
>       NSString *	 mobileCountryCode =  (*old_mobileCountryCode)();
>       if(NG_mobileCountryCode){
>           return NG_mobileCountryCode;
>       }
>       return mobileCountryCode;
>   }
>   
>   NSString	*(*old_mobileNetworkCode)();
>   
>   
>   NSString *	 new_mobileNetworkCode()
>   {
>       NSString *	 mobileCountryCode =  (*old_mobileNetworkCode)();
>       if(NG_mobileNetworkCode){
>           return NG_mobileNetworkCode;
>       }
>       return mobileCountryCode;
>   }
>   
>   NSString	*(*old_isoCountryCode)();
>   
>   
>   NSString *	 new_isoCountryCode()
>   {
>       NSString *	 mobileCountryCode =  (*old_isoCountryCode)();
>       if(NG_isoCountryCode){
>           return NG_isoCountryCode;
>       }
>       return mobileCountryCode;
>   }
>   NSString	*(*old_currentRadioAccessTechnology)();
>   
>   
>   NSString *	 new_currentRadioAccessTechnology()
>   {
>   
>       NSString *content = nil;
>       if(old_currentRadioAccessTechnology()){
>           content = NG_IMEI;
>   
>       }
>       return content;
>   }
>   
>   
>   
>   BOOL  (*old_UIApplicationDelegate)(id dd, SEL _cmd,UIApplication* application,  NSDictionary * launchOptions) NS_AVAILABLE_IOS(3_0);
>   
>   BOOL new_UIApplicationDelegate(id dd, SEL _cmd,UIApplication* application,  NSDictionary * launchOptions) NS_AVAILABLE_IOS(3_0){
>   
>   //    NSLog(@"-------------------------------------application = %@", application);
>   //    NSLog(@"-------------------------------------launchOptions = %@", launchOptions);
>   //
>   //    NSString *systemContent = nil;
>   //    id CFBundleIdentifier = [[[NSBundle mainBundle] infoDictionary] objectForKeyedSubscript:@"CFBundleIdentifier"];
>   //    NSString * sysVersion = [NG_systemVersion stringByReplacingOccurrencesOfString:@"." withString:@"_"];
>   //    if(CFBundleIdentifier && [CFBundleIdentifier isEqualToString:@"com.apple.mobilesafari"]){
>   //        systemContent = [NSString stringWithFormat:@"Mozilla/5.0 (%@; CPU iPhone OS %@ like Mac OS X) AppleWebKit/%@ (KHTML, like Gecko) Version/%@ Mobile/%@ Safari/%@",
>   //                NG_Product,sysVersion,@"602.1.50",@"10.0",NG_systemVersionWithEnglist,@"602.1"];
>   //
>   //    }else{
>   //        systemContent = [NSString stringWithFormat:@"Mozilla/5.0 (%@; CPU iPhone OS %@ like Mac OS X) AppleWebKit/%@ (KHTML, like Gecko) Mobile/%@",
>   //                        NG_Product,sysVersion,@"602.1.50",NG_systemVersionWithEnglist];
>   //    }
>   //
>   //    [[NSUserDefaults standardUserDefaults] registerDefaults:@{@"UserAgent":systemContent} ];
>   //    NSLog(@"----------------------------------------------------------");
>       return (*old_UIApplicationDelegate)(dd , _cmd , application,launchOptions);
>   
>   }
>   
>   
>   
>   
>   id <NSTextStorageDelegate>	(*old_currentRadioAccessTechnology_de)(id dd, SEL _cmd,id ss);
>   
>   
>   id <NSTextStorageDelegate> 	 new_currentRadioAccessTechnology_de(id dd, SEL _cmd,id ss)
>   {
>       MSHookMessageEx([ss class], @selector(application:didFinishLaunchingWithOptions:), (IMP)new_UIApplicationDelegate, (IMP*)&old_UIApplicationDelegate);
>       return old_currentRadioAccessTechnology_de(dd,_cmd,ss);
>   }
>   
>   int  *(*old_UIApplicationMain)(int argc, char *argv[], NSString * __nullable principalClassName, NSString * __nullable delegateClassName);
>   
>   int *new_UIApplicationMain(int argc, char *argv[], NSString * __nullable principalClassName, NSString * __nullable delegateClassName){
>       MSHookMessageEx([UIApplication class], @selector(setDelegate:), (IMP)new_currentRadioAccessTechnology_de, (IMP*)&old_currentRadioAccessTechnology_de);
>       return old_UIApplicationMain(argc,argv,principalClassName,delegateClassName);
>   }
>   
>   
>   Boolean
>   *(*old_SCNetworkReachabilityGetFlags)			(
>           SCNetworkReachabilityRef	target,
>           SCNetworkReachabilityFlags	*flags
>   )				__OSX_AVAILABLE_STARTING(__MAC_10_3,__IPHONE_2_0);
>   
>   Boolean
>   *new_SCNetworkReachabilityGetFlags			(
>           SCNetworkReachabilityRef	target,
>           SCNetworkReachabilityFlags	*flags
>   )				__OSX_AVAILABLE_STARTING(__MAC_10_3,__IPHONE_2_0){
>       *flags |= 0x40000u;
>       return old_SCNetworkReachabilityGetFlags(target,flags);
>   }
>   
>   
>   
>   
>   static CFStringRef (*old_MGCA)(CFStringRef Key);
>   CFStringRef new_MGCA(CFStringRef Key){
>       CFStringRef Ret=old_MGCA(Key);
>   
>   
>   
>   
>   
>       //设置Mac
>       if(CFEqual(Key, CFSTR("gI6iODv8MZuiP0IA+efJCw"))
>               || CFEqual(Key, CFSTR("WifiAddress"))){
>   
>           if(NG_macAdress){
>               return (CFStringRef)NG_macAdress;
>           }
>       }
>       //设置IMEI
>   //    if(CFEqual(Key,CFSTR( ""))){
>   //
>   //        return
>   //
>   //    }
>       // 设置设备类型 iPhone
>   //    if(CFEqual(Key, CFSTR("device-name-localized"))
>   //            ||CFEqual(Key, CFSTR("DeviceClass"))
>   //            ||CFEqual(Key, CFSTR("DeviceName")) ){
>   //        if(NG_phoneType){
>   //            return (CFStringRef)NG_phoneType;
>   //        }
>   //
>   //
>   //    }
>   //    // 设备序列号
>       if(CFEqual(Key, CFSTR("VasUgeSzVyHdB27g2XpN0g"))
>               || CFEqual(Key, CFSTR("SerialNumber")) ){
>   //        NSLog(@"-----------《《《《《进入》》》》》-----------");
>           if(NG_searnalNumber){
>               return (CFStringRef)NG_searnalNumber;
>           }
>   
>       }
>   
>   
>   
>   
>       // 设备蓝牙
>       if(CFEqual(Key, CFSTR("k5lVWbXuiZHLA17KGiVUAA"))
>               ||CFEqual(Key, CFSTR("BluetoothAddress")) ){
>           if(NG_BLUETOOTH){
>               return (CFStringRef)NG_BLUETOOTH;
>           }
>   //        NSLog(@"-----------《《《《《进入》》》》》-----------");
>       }
>       //唯一码 UUID
>       if( CFEqual(Key,CFSTR("UniqueDeviceID") ) ){
>           // 接是value
>   //        return CFBridgingRetain(@"3c71103a7002ce6800e40525d3f63377892c26c7");
>   //        NSLog(@"-----------《《《《《进入》》》》》-----------");
>           if(NG_UDID){
>               return (CFStringRef)NG_UDID;
>           }
>       }
>   
>   //    if( CFEqual(Key,CFSTR("nFRqKto/RuQAV1P+0/qkBA") ) ){
>   //        //是data 一
>   //
>   //
>   //
>   //
>   //
>   //        NSLog(@"---------《《 nFRqKto/RuQAV1P+0/qkBA 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("SIMTrayStatus") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 SIMTrayStatus 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("ModelNumber") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 ModelNumber 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("MLBSerialNumber") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 MLBSerialNumber 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("UniqueDeviceIDData") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 UniqueDeviceIDData 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("UniqueChipID") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 UniqueChipID 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("InverseDeviceID") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 InverseDeviceID 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("DiagData") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 DiagData 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("DieId") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 DieId 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("CPUArchitecture") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 CPUArchitecture 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("PartitionType") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 PartitionType 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("UserAssignedDeviceName") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 UserAssignedDeviceName 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("ActiveWirelessTechnology") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 ActiveWirelessTechnology 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("WifiAddressData") ) ){
>   //        //是data 一
>   //        NSLog(@"---------《《 WifiAddressData 》》----------------------Ret = %@", Ret);
>   //
>   //
>   //    }
>   //    if( CFEqual(Key,CFSTR("WifiVendor") ) ){
>   //        //是data 一
>   //
>   //        NSLog(@"---------《《 WifiVendor 》》----------------------Ret = %@", Ret);
>   //
>   //    }
>       //设备的版本
>       if(CFEqual(Key, CFSTR("BuildVersion")) ){
>           //13F69
>           if(NG_systemVersionWithEnglist){
>               return (CFStringRef)NG_systemVersionWithEnglist;
>           }
>   
>       }
>       //设备生产版本
>       if(CFEqual(Key, CFSTR("ProductVersion")) ){
>           //9.3.2
>           if(NG_systemVersion){
>               return (CFStringRef)NG_systemVersion;
>           }
>   
>       }
>       //设备生产种类
>       if(CFEqual(Key, CFSTR("ProductType")) ){
>           //iPhone6,1
>           if(NG_DEVICE_ProductType){
>               return (CFStringRef)NG_DEVICE_ProductType;
>           }
>   
>       }
>       // 是否为平板
>       if(CFEqual(Key, CFSTR("ipad")) ){
>           //0
>   
>   
>       }
>       // 设备型号
>       if(CFEqual(Key, CFSTR("ModelNumber")) ){
>           //ME337
>           if(NG_DEVICE_NAME){
>               return (CFStringRef)NG_DEVICE_NAME;
>           }
>       }
>       // 地域信息
>       if(CFEqual(Key, CFSTR("RegionInfo")) ){
>           //J/A
>           if(NG_DEVICE_LOCATION){
>               return (CFStringRef)NG_DEVICE_LOCATION;
>           }
>   
>       }
>       //设备所属国家
>       if(CFEqual(Key, CFSTR("h63QSdBCiT/z0WU6rdQv6Q")) ){
>           //J
>           if(NG_DEVICE_SINGLENAME){
>               return (CFStringRef)NG_DEVICE_SINGLENAME;
>           }
>   
>       }
>       // 设备名
>       if(CFEqual(Key, CFSTR("UserAssignedDeviceName")) ){
>           //Ggf
>           if(NG_deviceName){
>               return (CFStringRef)NG_deviceName;
>           }
>       }
>   
>       // 集合列表
>   //    if(CFEqual(Key, CFSTR("oPeik/9e8lQWMszEjbPzng")) ){
>   //        //Return Value:{
>   ////        ArtworkDeviceIdiom = phone;
>   ////        ArtworkDeviceProductDescription = "iPhone 5s";
>   ////        ArtworkDeviceScaleFactor = 2;
>   ////        ArtworkDeviceSubType = 568;
>   ////        CompatibleDeviceFallback = 0;
>   ////        DevicePerformanceMemoryClass = 1;
>   ////        GraphicsFeatureSetClass = "MTL1,2";
>   ////        GraphicsFeatureSetFallbacks = "GLES2,0";
>   //
>   //
>   //
>   //    }
>   
>   //    // 未判定的
>       if(CFEqual(Key, CFSTR("HWModelStr")) ){
>           //N51AP
>   
>           if(NG_DEVICE_HardwareModel){
>               return (CFStringRef)NG_DEVICE_HardwareModel;
>           }
>       }
>       if(CFEqual(Key, CFSTR("HardwarePlatform")) ){
>           //s5l8960x
>   
>           if(NG_DEVICE_HardwarePlatform){
>               return (CFStringRef)NG_DEVICE_HardwarePlatform;
>           }
>       }
>   //
>   //    if(CFEqual(Key, CFSTR("eZS2J+wspyGxqNYZeZ/sbA")) ){
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   //
>   ////        return (CFStringRef)@"<eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f>";
>   ////        return (CFStringRef)@"<54724fa2 90d2>";
>   //
>   //    }
>   //    if(CFEqual(Key, CFSTR("jSDzacs4RYWnWxn142UBLQ")) ){
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   //
>   ////        return (CFStringRef)@"<eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f>";
>   ////        return (CFStringRef)@"<54724fa2 90d2>";
>   //
>   //    }
>   //    if(CFEqual(Key, CFSTR("nFRqKto/RuQAV1P+0/qkBA")) ){
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   //
>   ////        return (CFStringRef)@"<eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f>";
>   //
>   //    }
>   //    if(CFEqual(Key,CFSTR( "oBbtJ8x+s1q0OkaiocPuog")) ){
>   //        //data
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   //
>   //        return (CFStringRef)@"<80020000 70040000 46010000 00000040 00000000 05000000>";
>   //
>   //    }
>   //
>   //    if(CFEqual(Key,CFSTR( "nFRqKto/RuQAV1P+0/qkBA")) ){
>   //        //data
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   ////        return (CFStringRef)@"<eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f>";
>   ////        return (CFStringRef)@"<eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f>";
>   //
>   //    }
>   //    if(CFEqual(Key,CFSTR( "TF31PAB6aO8KAbPyNKSxKA")) ){
>   //        //4903437917520
>   //
>   //
>   ////        NSLog(@"-------------------名字:《  %@  》-----Return Value:《  %@  》---",  Key,[[NSString alloc] initWithData: hexStringToByte((NSString *)Ret) encoding:NSUTF8StringEncoding]);
>   //        return (CFStringRef)@"<4903437917520>";
>   //    }
>   
>   //    NSLog(@"------------------转换后的内容为----------------- %@",[[NSString alloc] initWithData: hexStringToByte(@"eadd9a1c d078a359 13ce228d d14bdedf c7ea3f5f") encoding:NSUTF8StringEncoding] );
>   
>   //    NSLog(@"---------------------------名字:《  %@\n  》------------------------------------------------------------------------------Return Value:《  %@  》",Key,Ret);
>       return Ret;
>   }
>   
>   
>   
>   
>   
>   
>   // 进行初始化当前的设
>   - (void)ng_initChangeDeviceCode {
>       // 获取当前是否为工作状态
>       if([GeneralUtil isWorking]){
>           [self ng_changeCode];
>       }
>   
>   }
>   
>   -(void)ng_changeCode{
>       //获取Bundid 只有Preference  SpringBoard 和 AppStore 和 ItunsStrore才可以进行改码
>       NSString *bunId = [[NSBundle mainBundle] bundleIdentifier];
>   
>       // [GeneralUtil convertHexStrToString:NG_BUNID_Preferences]
>   
>       if(!bunId
>           || !([bunId isEqualToString: [GeneralUtil convertHexStrToString:NG_BUNID_Preferences]]
>           || [bunId isEqualToString:[GeneralUtil convertHexStrToString:NG_BUNID_AppStore]]
>           || [bunId isEqualToString:[GeneralUtil convertHexStrToString:NG_BUNID_itunesstored]]
>           || [bunId isEqualToString:[GeneralUtil convertHexStrToString:NG_BUNID_itunescloud]]
>           || [bunId isEqualToString:[GeneralUtil convertHexStrToString:NG_BUNID_SpringBoard]]
>           || [bunId isEqualToString:[GeneralUtil convertHexStrToString:NG_BUNID_springboard]])){
>           return;
>       }
>   
>       [self ng_getDomainUserSettingsParams];
>       // 设置各个版本对应的内容
>       NG_systemVersionWithNumber          = [self ng_changeDeviceVersion:NG_systemVersion];
>       NG_systemVersionWithEnglist         = [self ng_changeDeviceVersionToSpec:NG_systemVersion];
>       NG_systemVersionWithNumber_two      = [self ng_changeDeviceVersion:NG_systemVersionWithNumber];
>       // 设备名字 eg:ME337
>       NG_DEVICE_NAME                      = [self InterceptDeviceWithName:NG_UNITTYPE];
>       // 设备定力位置  eg: J/A
>       NG_DEVICE_LOCATION                  = [self InterceptDeviceWithLocation:NG_UNITTYPE];
>       // 设备地理位置  eg: J
>       NG_DEVICE_SINGLENAME                = [self InterceptDeviceWithSingleLocation:NG_UNITTYPE];
>       // 设置 改码Hook内容
>       MSHookFunction(&IORegistryEntryCreateCFProperty, &new_IORegistryEntryCreateCFProperties, (void**)&old_IORegistryEntryCreateCFProperties);
>       MSHookFunction(&IORegistryEntrySearchCFProperty, &new_IORegistryEntrySearchCFProperty, (void**)&old_IORegistryEntrySearchCFProperty);
>       MSHookFunction(&CNCopyCurrentNetworkInfo, &new_CNCopyCurrentNetworkInfo, (void**)&old_CNCopyCurrentNetworkInfo);
>       MSHookFunction(&CNCopySupportedInterfaces, &new_CNCopySupportedInterfaces, (void**)&old_CNCopySupportedInterfaces);
>       MSHookFunction(&SCNetworkReachabilityGetFlags, &new_SCNetworkReachabilityGetFlags, (void**)&old_SCNetworkReachabilityGetFlags);
>       MSHookFunction(&_CTServerConnectionCopyMobileIdentity, &new_CTServerConnectionCopyMobileIdentity, (void**)&old_CTServerConnectionCopyMobileIdentity);
>       MSHookFunction(&uname, &new_uname, (void**)&old_uname);
>       MSHookFunction(&sysctlnametomib, &new_sysctlnametomib, (void**)&old_sysctlnametomib);
>   //////    MSHookFunction(&sysctlbyname, &new_sysctlbyname, (void**)&old_sysctlbyname);
>   //////    MSHookFunction(&sysctl, &new_sysctl, (void**)&old_sysctl);
>       Class deviceClass = [UIDevice class];
>       //NSSelectorFromString(@"currentRadioAccessTechnology")
>   
>       MSHookMessageEx(deviceClass, NSSelectorFromString(@"model"), (IMP)new_mode, (IMP*)&old_mode);
>       MSHookMessageEx(deviceClass, NSSelectorFromString(@"localizedModel"), (IMP)&new_localizedModel, (IMP*)&old_localizedModel);
>       MSHookMessageEx(deviceClass, NSSelectorFromString(@"systemVersion"), (IMP)new_systemVersion, (IMP*)&old_systemVersion);
>       MSHookMessageEx(deviceClass, NSSelectorFromString(@"name"), (IMP)new_name, (IMP*)&old_name);
>       MSHookMessageEx(deviceClass, NSSelectorFromString(@"identifierForVendor"), (IMP)new_identifierForVendor, (IMP*)&old_identifierForVendor);
>       Class ASIdentifierManagerClass = objc_getClass("ASIdentifierManager");
>       MSHookMessageEx(ASIdentifierManagerClass,NSSelectorFromString(@"advertisingIdentifier"), (IMP)new_advertisingIdentifier, (IMP*)&old_advertisingIdentifier);
>       Class CTCarrierClass = objc_getClass("CTCarrier");
>       MSHookMessageEx(CTCarrierClass, NSSelectorFromString(@"carrierName"), (IMP)new_carrierName, (IMP*)&old_carrierName);
>       MSHookMessageEx(CTCarrierClass, NSSelectorFromString(@"mobileCountryCode"), (IMP)new_mobileCountryCode, (IMP*)&old_mobileCountryCode);
>       MSHookMessageEx(CTCarrierClass, NSSelectorFromString(@"mobileNetworkCode"), (IMP)new_mobileNetworkCode, (IMP*)&old_mobileNetworkCode);
>       MSHookMessageEx(CTCarrierClass, NSSelectorFromString(@"isoCountryCode"), (IMP)new_isoCountryCode, (IMP*)&old_isoCountryCode);
>       Class CTTelephonyNetworkInfoClass = objc_getClass("CTTelephonyNetworkInfo");
>       MSHookMessageEx(CTTelephonyNetworkInfoClass, NSSelectorFromString(@"currentRadioAccessTechnology"), (IMP)new_currentRadioAccessTechnology, (IMP*)&old_currentRadioAccessTechnology);
>       MSHookFunction(&UIApplicationMain , &new_UIApplicationMain, (void**)&old_UIApplicationMain);
>   //
>   
>       void *Symbol = MSFindSymbol(MSGetImageByName("/usr/lib/libMobileGestalt.dylib"), "_MGCopyAnswer");
>   //    NSLog(@"MG: %p", Symbol);
>       csh handle;
>       cs_insn *insn;
>       cs_insn BLInstruction;
>       size_t count;
>       unsigned long realMGAddress = 0;
>       //MSHookFunction(Symbol,(void*)new_MGCA, (void**)&old_MGCA);
>       if (cs_open(CS_ARCH_ARM64, CS_MODE_ARM, &handle) == CS_ERR_OK) {
>           /*cs_disasm(csh handle,
>                   const uint8_t *code, size_t code_size,
>                   uint64_t address,
>                   size_t count,
>                   cs_insn **insn);*/
>           count = cs_disasm(handle, (const uint8_t *) Symbol, 0x1000, (uint64_t) Symbol, 0, &insn);
>           if (count > 0) {
>               NSLog(@"Found %lu instructions", count);
>               for (size_t j = 0; j < count; j++) {
>                   NSLog(@"0x%" PRIx64 ":\t%s\t\t%s\n", insn[j].address, insn[j].mnemonic, insn[j].op_str);
>                   if (insn[j].id == ARM64_INS_B) {
>                       BLInstruction = insn[j];
>                       sscanf(BLInstruction.op_str, "#%lx", &realMGAddress);
>                       break;
>                   }
>               }
>               cs_free(insn, count);
>           } else {
>               NSLog(@"ERROR: Failed to disassemble given code!%i \n", cs_errno(handle));
>           }
>           cs_close(&handle);
>       }
>       //Now perform actual hook
>       MSHookFunction((void *) realMGAddress, (void *) new_MGCA, (void **) &old_MGCA);
>   
>   }
>   
>   
>   
>   /**
>    * 进行更改当前设备的版本信息
>    * */
>   -(NSString *)ng_changeDeviceVersion:(NSString *)version{
>       if(!version){
>           return nil;
>       }
>       if([version isEqualToString:@"6.1.6"]){
>           return @"13.4.0";
>       }
>       if([version hasPrefix:@"6."]){
>           return @"13.0.0";
>       }
>       if([version hasPrefix:@"7."]){
>           return @"14.0.0";
>       }
>       if([version hasPrefix:@"8."]){
>           return @"14.5.0";
>       }
>       if([version isEqualToString:@"9.3.3"] || [version isEqualToString:@"9.3.4"] || [version isEqualToString:@"9.3.5"]){
>           return @"15.6.0";
>       }
>       if([version hasPrefix:@"9."]){
>           return @"15.0.0";
>   
>       } if([version hasPrefix:@"10."]){
>           return @"16.0.0";
>   
>       } if([version hasPrefix:@"10.1"]){
>           return @"16.1.0";
>   
>       } if([version hasPrefix:@"10.2"]){
>           return @"16.3.0";
>       }
>       return nil;
>   }
>   
>   
>   /**
>    * 进行更改当前设备的版本版本信息转化为IOS公司特有的  VC为此数据
>    * */
>   -(NSString *)ng_changeDeviceVersionToSpec:(NSString *)version{
>       if(!version){
>           return nil;
>       }
>       NSDictionary *DIC =@{@"6.0":@"10A403",
>               @"6.0.1"    :@"10A523",
>               @"6.0.2"    :@"10A551",
>               @"6.1"      :@"10B141",
>               @"6.1.1"    :@"10B145",
>               @"6.1.2"    :@"10B146",
>               @"6.1.3"    :@"10B329",
>               @"6.1.4"    :@"10B350",
>               @"6.1.5"    :@"10B400",
>               @"6.1.6"    :@"10B500",
>               @"7.0"      :@"11A465",
>               @"7.0.1"    :@"11A470a",
>               @"7.0.2"    :@"11A501",
>               @"7.0.3"    :@"11B511",
>               @"7.0.4"    :@"11B553",
>               @"7.1"      :@"11D167",
>               @"7.1.1"    :@"11D201",
>               @"7.1.2"    :@"11D257",
>               @"8.0"      :@"12A365",
>               @"8.0.1"    :@"12A402",
>               @"8.0.2"    :@"12A405",
>               @"8.1"      :@"12B410",
>               @"8.1.1"    :@"12B435",
>               @"8.1.2"    :@"12B440",
>               @"8.1.3"    :@"12B466",
>               @"8.2"      :@"12D508",
>               @"8.3"      :@"12F70",
>               @"8.4"      :@"12H143",
>               @"9.0"      :@"13A344",
>               @"9.0.1"    :@"13A404",
>               @"9.0.2"    :@"13A452",
>               @"9.1"      :@"13B143",
>               @"9.2"      :@"13C75",
>               @"9.2.1"    :@"13D15",
>               @"9.3"      :@"13E233",
>               @"9.3.1"    :@"13E238",
>               @"9.3.2"    :@"13F69",
>               @"9.3.3"    :@"13G34",
>               @"9.3.4"    :@"13G35",
>               @"9.3.5"    :@"13G36",
>               @"10.3.3"   :@"14G60",
>               @"10.0.2"   :@"14A456",
>               @"10.0.1"   :@"14A403",
>               @"10.0.3"   :@"14A551",
>               @"10.1"     :@"14B72c",
>               @"10.1.1"   :@"14B150",
>               @"10.2"     :@"14C92",
>               @"10.2.1"   :@"14D27",
>               @"10.3"     :@"14E277",
>               @"10.3.1"   :@"14E304",
>               @"10.3.2"   :@"14F89",
>               @"11.0"     :@"15A372",
>               @"11.0.1"   :@"15A402",
>               @"11.0.2"   :@"15A421",
>               @"11.0.3"   :@"15A432",
>               @"11.1"     :@"15B93",
>               @"11.1.1"   :@"15B202",
>               @"11.1.2"   :@"15B202",
>               @"11.2"     :@"15C114",
>       };
>   
>   
>       return [DIC objectForKeyedSubscript:version];
>   
>   
>   
>   }
>   
>   @end
>   © 2018 GitHub, Inc.
>   Terms
>   Privacy
>   Security
>   Status
>   Help
>   Contact GitHub
>   API
>   Training
>   Shop
>   Blog
>   About
>   Press h to open a hovercard with more details.
>   ```
>
>   
>
> * [UIDevice%2BExtensions.mm](https://github.com/zhangkn/knaso/blob/master/ASO/ChangeCode/UIDevice%2BExtensions.mm)
>
>   ```objc
>   #import "UIDevice+Extensions.h"
>   #include <sys/types.h>
>   #include <sys/sysctl.h>
>   #import <mach/mach_host.h>
>   #include <netinet/in.h>
>   #include <arpa/inet.h>
>   #include <netdb.h>
>   #include <ifaddrs.h>
>   #include <sys/socket.h>
>   #include <net/if.h>
>   #include <net/if_dl.h>
>   #include <ifaddrs.h>
>   
>   #if SUPPORTS_IOKIT_EXTENSIONS
>   #pragma mark IOKit miniheaders
>   
>   #define kIODeviceTreePlane        "IODeviceTree"
>   
>   enum {
>       kIORegistryIterateRecursively    = 0x00000001,
>       kIORegistryIterateParents        = 0x00000002
>   };
>   
>   typedef mach_port_t    io_object_t;
>   typedef io_object_t    io_registry_entry_t;
>   typedef char        io_name_t[128];
>   typedef UInt32        IOOptionBits;
>   
>   CFTypeRef
>   IORegistryEntrySearchCFProperty(
>           io_registry_entry_t    entry,
>           const io_name_t        plane,
>           CFStringRef        key,
>           CFAllocatorRef        allocator,
>           IOOptionBits        options );
>   
>   kern_return_t
>   IOMasterPort( mach_port_t    bootstrapPort,
>           mach_port_t *    masterPort );
>   
>   io_registry_entry_t
>   IORegistryGetRootEntry(
>           mach_port_t    masterPort );
>   
>   CFTypeRef
>   IORegistryEntrySearchCFProperty(
>           io_registry_entry_t    entry,
>           const io_name_t        plane,
>           CFStringRef        key,
>           CFAllocatorRef        allocator,
>           IOOptionBits        options );
>   
>   kern_return_t   mach_port_deallocate
>           (ipc_space_t          task,
>                   mach_port_name_t  name);
>   
>   
>   
>   @implementation UIDevice (Extensions)
>   
>   #pragma mark IOKit Utils
>   NSArray *getValue(NSString *iosearch)
>   {
>       mach_port_t          masterPort;
>       CFTypeID             propID = (CFTypeID) NULL;
>       unsigned int         bufSize;
>   
>       kern_return_t kr = IOMasterPort(MACH_PORT_NULL, &masterPort);
>       if (kr != noErr) return nil;
>   
>       io_registry_entry_t entry = IORegistryGetRootEntry(masterPort);
>       if (entry == MACH_PORT_NULL) return nil;
>   
>       CFTypeRef prop = IORegistryEntrySearchCFProperty(entry, kIODeviceTreePlane, (CFStringRef) iosearch, nil, kIORegistryIterateRecursively);
>       if (!prop) return nil;
>   
>       propID = CFGetTypeID(prop);
>       if (!(propID == CFDataGetTypeID()))
>       {
>           mach_port_deallocate(mach_task_self(), masterPort);
>           return nil;
>       }
>   
>       CFDataRef propData = (CFDataRef) prop;
>       if (!propData) return nil;
>   
>       bufSize = CFDataGetLength(propData);
>       if (!bufSize) return nil;
>   
>       NSString *p1 = [[[NSString alloc] initWithBytes:CFDataGetBytePtr(propData) length:bufSize encoding:1] autorelease];
>       mach_port_deallocate(mach_task_self(), masterPort);
>       return [p1 componentsSeparatedByString:@"/0"];
>   }
>   
>   
>   - (NSString *)imei {
>       NSArray *results = getValue(@"device-imei");
>       if (results) return [results objectAtIndex:0];
>       return nil;
>   }
>   
>   - (NSString *)serialnumber {
>       NSArray *results = getValue(@"serial-number");
>       if (results) return [results objectAtIndex:0];
>       return nil;
>   }
>   
>   - (NSString *)backlightlevel {
>       NSArray *results = getValue(@"backlight-level");
>       if (results) return [results objectAtIndex:0];
>       return nil;
>   }
>   
>   
>   @end
>   #endif
>   
>   ```
>
>   



#### UIDevice+Extensions.h: uniqueAppInstanceIdentifier

```
#import "UIDevice+Extensions.h"

@implementation UIDevice (org_apache_cordova_UIDevice_Extension)

- (NSString*)uniqueAppInstanceIdentifier
{
    NSUserDefaults* userDefaults = [NSUserDefaults standardUserDefaults];
    static NSString* UUID_KEY = @"CDVUUID";

    NSString* app_uuid = [userDefaults stringForKey:UUID_KEY];

    if (app_uuid == nil) {
        CFUUIDRef uuidRef = CFUUIDCreate(kCFAllocatorDefault);
        CFStringRef uuidString = CFUUIDCreateString(kCFAllocatorDefault, uuidRef);

        app_uuid = [NSString stringWithString:(__bridge NSString*)uuidString];
        [userDefaults setObject:app_uuid forKey:UUID_KEY];
        [userDefaults synchronize];

        CFRelease(uuidString);
        CFRelease(uuidRef);
    }

    return app_uuid;
}

@end
```



####   #import "UIDevice+YYAdd.h" 



```objc
//
//  UIDevice+YYAdd.m
//  YYKit <https://github.com/ibireme/YYKit>
//
//  Created by ibireme on 13/4/3.
//  Copyright (c) 2015 ibireme.
//
//  This source code is licensed under the MIT-style license found in the
//  LICENSE file in the root directory of this source tree.
//

#import "UIDevice+YYAdd.h"
#include <sys/socket.h>
#include <sys/sysctl.h>
#include <net/if.h>
#include <net/if_dl.h>
#include <mach/mach.h>
#include <arpa/inet.h>
#include <ifaddrs.h>
#import "YYKitMacro.h"
#import "NSString+YYAdd.h"

YYSYNTH_DUMMY_CLASS(UIDevice_YYAdd)


@implementation UIDevice (YYAdd)

+ (double)systemVersion {
    static double version;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        version = [UIDevice currentDevice].systemVersion.doubleValue;
    });
    return version;
}

- (BOOL)isPad {
    static dispatch_once_t one;
    static BOOL pad;
    dispatch_once(&one, ^{
        pad = UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad;
    });
    return pad;
}

- (BOOL)isSimulator {
#if TARGET_OS_SIMULATOR
    return YES;
#else
    return NO;
#endif
}

- (BOOL)isJailbroken {
    if ([self isSimulator]) return NO; // Dont't check simulator
    
    // iOS9 URL Scheme query changed ...
    // NSURL *cydiaURL = [NSURL URLWithString:@"cydia://package"];
    // if ([[UIApplication sharedApplication] canOpenURL:cydiaURL]) return YES;
    
    NSArray *paths = @[@"/Applications/Cydia.app",
                       @"/private/var/lib/apt/",
                       @"/private/var/lib/cydia",
                       @"/private/var/stash"];
    for (NSString *path in paths) {
        if ([[NSFileManager defaultManager] fileExistsAtPath:path]) return YES;
    }
    
    FILE *bash = fopen("/bin/bash", "r");
    if (bash != NULL) {
        fclose(bash);
        return YES;
    }
    
    NSString *path = [NSString stringWithFormat:@"/private/%@", [NSString stringWithUUID]];
    if ([@"test" writeToFile : path atomically : YES encoding : NSUTF8StringEncoding error : NULL]) {
        [[NSFileManager defaultManager] removeItemAtPath:path error:nil];
        return YES;
    }
    
    return NO;
}

#ifdef __IPHONE_OS_VERSION_MIN_REQUIRED
- (BOOL)canMakePhoneCalls {
    __block BOOL can;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        can = [[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"tel://"]];
    });
    return can;
}
#endif

- (NSString *)ipAddressWithIfaName:(NSString *)name {
    if (name.length == 0) return nil;
    NSString *address = nil;
    struct ifaddrs *addrs = NULL;
    if (getifaddrs(&addrs) == 0) {
        struct ifaddrs *addr = addrs;
        while (addr) {
            if ([[NSString stringWithUTF8String:addr->ifa_name] isEqualToString:name]) {
                sa_family_t family = addr->ifa_addr->sa_family;
                switch (family) {
                    case AF_INET: { // IPv4
                        char str[INET_ADDRSTRLEN] = {0};
                        inet_ntop(family, &(((struct sockaddr_in *)addr->ifa_addr)->sin_addr), str, sizeof(str));
                        if (strlen(str) > 0) {
                            address = [NSString stringWithUTF8String:str];
                        }
                    } break;
                        
                    case AF_INET6: { // IPv6
                        char str[INET6_ADDRSTRLEN] = {0};
                        inet_ntop(family, &(((struct sockaddr_in6 *)addr->ifa_addr)->sin6_addr), str, sizeof(str));
                        if (strlen(str) > 0) {
                            address = [NSString stringWithUTF8String:str];
                        }
                    }
                        
                    default: break;
                }
                if (address) break;
            }
            addr = addr->ifa_next;
        }
    }
    freeifaddrs(addrs);
    return address;
}

- (NSString *)ipAddressWIFI {
    return [self ipAddressWithIfaName:@"en0"];
}

- (NSString *)ipAddressCell {
    return [self ipAddressWithIfaName:@"pdp_ip0"];
}


typedef struct {
    uint64_t en_in;
    uint64_t en_out;
    uint64_t pdp_ip_in;
    uint64_t pdp_ip_out;
    uint64_t awdl_in;
    uint64_t awdl_out;
} yy_net_interface_counter;


static uint64_t yy_net_counter_add(uint64_t counter, uint64_t bytes) {
    if (bytes < (counter % 0xFFFFFFFF)) {
        counter += 0xFFFFFFFF - (counter % 0xFFFFFFFF);
        counter += bytes;
    } else {
        counter = bytes;
    }
    return counter;
}

static uint64_t yy_net_counter_get_by_type(yy_net_interface_counter *counter, YYNetworkTrafficType type) {
    uint64_t bytes = 0;
    if (type & YYNetworkTrafficTypeWWANSent) bytes += counter->pdp_ip_out;
    if (type & YYNetworkTrafficTypeWWANReceived) bytes += counter->pdp_ip_in;
    if (type & YYNetworkTrafficTypeWIFISent) bytes += counter->en_out;
    if (type & YYNetworkTrafficTypeWIFIReceived) bytes += counter->en_in;
    if (type & YYNetworkTrafficTypeAWDLSent) bytes += counter->awdl_out;
    if (type & YYNetworkTrafficTypeAWDLReceived) bytes += counter->awdl_in;
    return bytes;
}

static yy_net_interface_counter yy_get_net_interface_counter() {
    static dispatch_semaphore_t lock;
    static NSMutableDictionary *sharedInCounters;
    static NSMutableDictionary *sharedOutCounters;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInCounters = [NSMutableDictionary new];
        sharedOutCounters = [NSMutableDictionary new];
        lock = dispatch_semaphore_create(1);
    });
    
    yy_net_interface_counter counter = {0};
    struct ifaddrs *addrs;
    const struct ifaddrs *cursor;
    if (getifaddrs(&addrs) == 0) {
        cursor = addrs;
        dispatch_semaphore_wait(lock, DISPATCH_TIME_FOREVER);
        while (cursor) {
            if (cursor->ifa_addr->sa_family == AF_LINK) {
                const struct if_data *data = cursor->ifa_data;
                NSString *name = cursor->ifa_name ? [NSString stringWithUTF8String:cursor->ifa_name] : nil;
                if (name) {
                    uint64_t counter_in = ((NSNumber *)sharedInCounters[name]).unsignedLongLongValue;
                    counter_in = yy_net_counter_add(counter_in, data->ifi_ibytes);
                    sharedInCounters[name] = @(counter_in);
                    
                    uint64_t counter_out = ((NSNumber *)sharedOutCounters[name]).unsignedLongLongValue;
                    counter_out = yy_net_counter_add(counter_out, data->ifi_obytes);
                    sharedOutCounters[name] = @(counter_out);
                    
                    if ([name hasPrefix:@"en"]) {
                        counter.en_in += counter_in;
                        counter.en_out += counter_out;
                    } else if ([name hasPrefix:@"awdl"]) {
                        counter.awdl_in += counter_in;
                        counter.awdl_out += counter_out;
                    } else if ([name hasPrefix:@"pdp_ip"]) {
                        counter.pdp_ip_in += counter_in;
                        counter.pdp_ip_out += counter_out;
                    }
                }
            }
            cursor = cursor->ifa_next;
        }
        dispatch_semaphore_signal(lock);
        freeifaddrs(addrs);
    }
    
    return counter;
}

- (uint64_t)getNetworkTrafficBytes:(YYNetworkTrafficType)types {
    yy_net_interface_counter counter = yy_get_net_interface_counter();
    return yy_net_counter_get_by_type(&counter, types);
}

- (NSString *)machineModel {
    static dispatch_once_t one;
    static NSString *model;
    dispatch_once(&one, ^{
        size_t size;
        sysctlbyname("hw.machine", NULL, &size, NULL, 0);
        char *machine = malloc(size);
        sysctlbyname("hw.machine", machine, &size, NULL, 0);
        model = [NSString stringWithUTF8String:machine];
        free(machine);
    });
    return model;
}

- (NSString *)machineModelName {
    static dispatch_once_t one;
    static NSString *name;
    dispatch_once(&one, ^{
        NSString *model = [self machineModel];
        if (!model) return;
        NSDictionary *dic = @{
            @"Watch1,1" : @"Apple Watch 38mm",
            @"Watch1,2" : @"Apple Watch 42mm",
            @"Watch2,3" : @"Apple Watch Series 2 38mm",
            @"Watch2,4" : @"Apple Watch Series 2 42mm",
            @"Watch2,6" : @"Apple Watch Series 1 38mm",
            @"Watch1,7" : @"Apple Watch Series 1 42mm",
            
            @"iPod1,1" : @"iPod touch 1",
            @"iPod2,1" : @"iPod touch 2",
            @"iPod3,1" : @"iPod touch 3",
            @"iPod4,1" : @"iPod touch 4",
            @"iPod5,1" : @"iPod touch 5",
            @"iPod7,1" : @"iPod touch 6",
            
            @"iPhone1,1" : @"iPhone 1G",
            @"iPhone1,2" : @"iPhone 3G",
            @"iPhone2,1" : @"iPhone 3GS",
            @"iPhone3,1" : @"iPhone 4 (GSM)",
            @"iPhone3,2" : @"iPhone 4",
            @"iPhone3,3" : @"iPhone 4 (CDMA)",
            @"iPhone4,1" : @"iPhone 4S",
            @"iPhone5,1" : @"iPhone 5",
            @"iPhone5,2" : @"iPhone 5",
            @"iPhone5,3" : @"iPhone 5c",
            @"iPhone5,4" : @"iPhone 5c",
            @"iPhone6,1" : @"iPhone 5s",
            @"iPhone6,2" : @"iPhone 5s",
            @"iPhone7,1" : @"iPhone 6 Plus",
            @"iPhone7,2" : @"iPhone 6",
            @"iPhone8,1" : @"iPhone 6s",
            @"iPhone8,2" : @"iPhone 6s Plus",
            @"iPhone8,4" : @"iPhone SE",
            @"iPhone9,1" : @"iPhone 7",
            @"iPhone9,2" : @"iPhone 7 Plus",
            @"iPhone9,3" : @"iPhone 7",
            @"iPhone9,4" : @"iPhone 7 Plus",
            
            @"iPad1,1" : @"iPad 1",
            @"iPad2,1" : @"iPad 2 (WiFi)",
            @"iPad2,2" : @"iPad 2 (GSM)",
            @"iPad2,3" : @"iPad 2 (CDMA)",
            @"iPad2,4" : @"iPad 2",
            @"iPad2,5" : @"iPad mini 1",
            @"iPad2,6" : @"iPad mini 1",
            @"iPad2,7" : @"iPad mini 1",
            @"iPad3,1" : @"iPad 3 (WiFi)",
            @"iPad3,2" : @"iPad 3 (4G)",
            @"iPad3,3" : @"iPad 3 (4G)",
            @"iPad3,4" : @"iPad 4",
            @"iPad3,5" : @"iPad 4",
            @"iPad3,6" : @"iPad 4",
            @"iPad4,1" : @"iPad Air",
            @"iPad4,2" : @"iPad Air",
            @"iPad4,3" : @"iPad Air",
            @"iPad4,4" : @"iPad mini 2",
            @"iPad4,5" : @"iPad mini 2",
            @"iPad4,6" : @"iPad mini 2",
            @"iPad4,7" : @"iPad mini 3",
            @"iPad4,8" : @"iPad mini 3",
            @"iPad4,9" : @"iPad mini 3",
            @"iPad5,1" : @"iPad mini 4",
            @"iPad5,2" : @"iPad mini 4",
            @"iPad5,3" : @"iPad Air 2",
            @"iPad5,4" : @"iPad Air 2",
            @"iPad6,3" : @"iPad Pro (9.7 inch)",
            @"iPad6,4" : @"iPad Pro (9.7 inch)",
            @"iPad6,7" : @"iPad Pro (12.9 inch)",
            @"iPad6,8" : @"iPad Pro (12.9 inch)",
            
            @"AppleTV2,1" : @"Apple TV 2",
            @"AppleTV3,1" : @"Apple TV 3",
            @"AppleTV3,2" : @"Apple TV 3",
            @"AppleTV5,3" : @"Apple TV 4",
            
            @"i386" : @"Simulator x86",
            @"x86_64" : @"Simulator x64",
        };
        name = dic[model];
        if (!name) name = model;
    });
    return name;
}

- (NSDate *)systemUptime {
    NSTimeInterval time = [[NSProcessInfo processInfo] systemUptime];
    return [[NSDate alloc] initWithTimeIntervalSinceNow:(0 - time)];
}

- (int64_t)diskSpace {
    NSError *error = nil;
    NSDictionary *attrs = [[NSFileManager defaultManager] attributesOfFileSystemForPath:NSHomeDirectory() error:&error];
    if (error) return -1;
    int64_t space =  [[attrs objectForKey:NSFileSystemSize] longLongValue];
    if (space < 0) space = -1;
    return space;
}

- (int64_t)diskSpaceFree {
    NSError *error = nil;
    NSDictionary *attrs = [[NSFileManager defaultManager] attributesOfFileSystemForPath:NSHomeDirectory() error:&error];
    if (error) return -1;
    int64_t space =  [[attrs objectForKey:NSFileSystemFreeSize] longLongValue];
    if (space < 0) space = -1;
    return space;
}

- (int64_t)diskSpaceUsed {
    int64_t total = self.diskSpace;
    int64_t free = self.diskSpaceFree;
    if (total < 0 || free < 0) return -1;
    int64_t used = total - free;
    if (used < 0) used = -1;
    return used;
}

- (int64_t)memoryTotal {
    int64_t mem = [[NSProcessInfo processInfo] physicalMemory];
    if (mem < -1) mem = -1;
    return mem;
}

- (int64_t)memoryUsed {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return page_size * (vm_stat.active_count + vm_stat.inactive_count + vm_stat.wire_count);
}

- (int64_t)memoryFree {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return vm_stat.free_count * page_size;
}

- (int64_t)memoryActive {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return vm_stat.active_count * page_size;
}

- (int64_t)memoryInactive {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return vm_stat.inactive_count * page_size;
}

- (int64_t)memoryWired {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return vm_stat.wire_count * page_size;
}

- (int64_t)memoryPurgable {
    mach_port_t host_port = mach_host_self();
    mach_msg_type_number_t host_size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t page_size;
    vm_statistics_data_t vm_stat;
    kern_return_t kern;
    
    kern = host_page_size(host_port, &page_size);
    if (kern != KERN_SUCCESS) return -1;
    kern = host_statistics(host_port, HOST_VM_INFO, (host_info_t)&vm_stat, &host_size);
    if (kern != KERN_SUCCESS) return -1;
    return vm_stat.purgeable_count * page_size;
}

- (NSUInteger)cpuCount {
    return [NSProcessInfo processInfo].activeProcessorCount;
}

- (float)cpuUsage {
    float cpu = 0;
    NSArray *cpus = [self cpuUsagePerProcessor];
    if (cpus.count == 0) return -1;
    for (NSNumber *n in cpus) {
        cpu += n.floatValue;
    }
    return cpu;
}

- (NSArray *)cpuUsagePerProcessor {
    processor_info_array_t _cpuInfo, _prevCPUInfo = nil;
    mach_msg_type_number_t _numCPUInfo, _numPrevCPUInfo = 0;
    unsigned _numCPUs;
    NSLock *_cpuUsageLock;
    
    int _mib[2U] = { CTL_HW, HW_NCPU };
    size_t _sizeOfNumCPUs = sizeof(_numCPUs);
    int _status = sysctl(_mib, 2U, &_numCPUs, &_sizeOfNumCPUs, NULL, 0U);
    if (_status)
        _numCPUs = 1;
    
    _cpuUsageLock = [[NSLock alloc] init];
    
    natural_t _numCPUsU = 0U;
    kern_return_t err = host_processor_info(mach_host_self(), PROCESSOR_CPU_LOAD_INFO, &_numCPUsU, &_cpuInfo, &_numCPUInfo);
    if (err == KERN_SUCCESS) {
        [_cpuUsageLock lock];
        
        NSMutableArray *cpus = [NSMutableArray new];
        for (unsigned i = 0U; i < _numCPUs; ++i) {
            Float32 _inUse, _total;
            if (_prevCPUInfo) {
                _inUse = (
                          (_cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_USER]   - _prevCPUInfo[(CPU_STATE_MAX * i) + CPU_STATE_USER])
                          + (_cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_SYSTEM] - _prevCPUInfo[(CPU_STATE_MAX * i) + CPU_STATE_SYSTEM])
                          + (_cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_NICE]   - _prevCPUInfo[(CPU_STATE_MAX * i) + CPU_STATE_NICE])
                          );
                _total = _inUse + (_cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_IDLE] - _prevCPUInfo[(CPU_STATE_MAX * i) + CPU_STATE_IDLE]);
            } else {
                _inUse = _cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_USER] + _cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_SYSTEM] + _cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_NICE];
                _total = _inUse + _cpuInfo[(CPU_STATE_MAX * i) + CPU_STATE_IDLE];
            }
            [cpus addObject:@(_inUse / _total)];
        }
        
        [_cpuUsageLock unlock];
        if (_prevCPUInfo) {
            size_t prevCpuInfoSize = sizeof(integer_t) * _numPrevCPUInfo;
            vm_deallocate(mach_task_self(), (vm_address_t)_prevCPUInfo, prevCpuInfoSize);
        }
        return cpus;
    } else {
        return nil;
    }
}

@end

```



# See Also 

>* [internal_DeviceInfo.h:taobeo4iphone]
>
>  ```
>  //
>  //     Generated by class-dump 3.5 (64 bit).
>  //
>  //     class-dump is Copyright (C) 1997-1998, 2000-2001, 2004-2013 by Steve Nygard.
>  //
>  
>  #import "NSObject.h"
>  
>  @class ALAssetsLibrary, NSMutableArray, NSMutableDictionary;
>  
>  @interface internal_DeviceInfo : NSObject
>  {
>      ALAssetsLibrary *_assetsLibrary;
>      NSMutableArray *_assets;
>      NSMutableDictionary *_record;
>  }
>  
>  + (id)getContactInfo;
>  + (id)getAvailableSensors;
>  + (id)defaultAssetsLibrary;
>  + (id)getSDKUpTimeInterval;
>  + (id)getSysUpTimeInterval;
>  + (id)getLanguage;
>  + (id)getTimeZone;
>  + (_Bool)checkPolicy:(id)arg1 selector:(SEL)arg2;
>  + (id)getFingerIdHashInfo;
>  + (id)getAppList:(id)arg1;
>  + (unsigned long long)getMemorySize;
>  + (id)getNetworkInfo;
>  + (id)getAppVersion;
>  + (id)getAppName;
>  + (id)getScreenHeight;
>  + (id)getScreenWidth;
>  + (id)getHWModel;
>  + (id)getKernHostName;
>  + (id)getSSIDInfo;
>  + (long long)getBatteryStatus;
>  + (float)getBatteryLevel;
>  + (id)getDiskFreeSpace;
>  + (id)getDiskSpace;
>  + (long long)getCpuCount;
>  + (long long)getCpuFreq;
>  + (id)getModelName;
>  + (id)internal_getAdvIdDisabled;
>  + (id)getAdvId;
>  + (id)getIdForVendor;
>  + (id)getDeviceName;
>  + (id)getOSName;
>  + (id)getOSVersion;
>  + (id)screenResolution;
>  + (_Bool)isEmulator;
>  + (unsigned short)isJailBreak;
>  + (id)getMCC;
>  + (id)getMNC;
>  + (id)carrierName;
>  + (unsigned long long)getSysInfo:(unsigned int)arg1;
>  + (id)getSysInfoByName:(char *)arg1;
>  + (id)getBundleId;
>  @property(retain, nonatomic) NSMutableDictionary *record; // @synthesize record=_record;
>  @property(retain, nonatomic) NSMutableArray *assets; // @synthesize assets=_assets;
>  @property(retain, nonatomic) ALAssetsLibrary *assetsLibrary; // @synthesize assetsLibrary=_assetsLibrary;
>  - (void).cxx_destruct;
>  - (void)getPhotoInfo:(CDUnknownBlockType)arg1;
>  
>  @end
>  
>  
>  ```
>
>  
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost UUIDString 设备udid的收集 -t UUID
> #原来""的参数，需要自己加上""
```

