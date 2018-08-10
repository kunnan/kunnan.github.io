---
layout: post
title: ethical-hacking-mobile-devices-and-platforms
date: 2018-08-10
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Application data storage；Stored Data Protection；Cached and temp data；URL handlers；Binary protection；iOS Security
---



# iOS applications

 

- iOS apps can interact only with directories stored within its sandbox

- during installation iOS generates UUID and creates content directories for the app inside the sandbox dirs.

  * depending on the iOS version, `bundle` & `data` dirs can be store in the same or different location (since iOS 8)

    * ios8

      ```
      /private/var/mobile/Containers/Data/Application/8C8AAA46-04A3-441B-B968-6EB09E07F134/tmp
      ```

      

   

  * `bundle container`- app container named `yourappname.app`. This folder is signed, and any changes to it will prevent app from running

  * `data container`- holds runtime data for both the app & the user

    * there can be other folders in `data container` which will be app specific ones.

      ```
       [NSKeyedArchiver archiveRootObject:<CLocalInfo: 0x15d75000> toFile:/var/mobile/Containers/Data/Application/8C8AAA46-04A3-441B-B968-6EB09E07F134/Library/LocalInfo.lst ]
      
      ```

      

    * this directory is further divided into other subfolders, which app can use store its data:

      * `Documents` user generated content
      * `Library` internal app data
      * `tmp` used to write temp data during the current app invocation

       

- in `bundle container` there’s `Info.plist`it’s a plain xml file which contains basic info about app required to run it:

  * name
  * ID
  * main executable
  * etc.

   

   

  # Application data storage

   There are three main storage options:

  * UIPasteboard 

    * 获取

      ```
      - (WLUserModel *)model{
          
          if (_model == nil) {
              
              
              
              /**
               
               
               应用级别的，数据在属于自己的应用内部共享；
               (默认情况下是不会把数据写进沙盒的，也就是说（复制、剪切）粘贴内容会因为应用的退出而销毁掉，我们可以设置相关属性 persistent值为 YES让其进行数据的持久化存储起来)
               
               
               Ps：其他的一些属性，我们可以点进去观察一下基本可以理解例如 persistent 是否进行数据持久化 还有 changeCount 改变次数（剪切板）系统重启方才重新计数
               
               */
              NSString *contentSign =@"";
              NSString *contentUserID =@"";
              UIPasteboard *pasteboard = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeySign create:NO];
              
              if (pasteboard){
                  
                  contentSign = pasteboard.string;
                  
                  
              }
              UIPasteboard *pasteboardUserID = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeyUserID create:NO];
              
              if (pasteboardUserID){
                  
                  contentUserID = pasteboardUserID.string;
                  
                  
              }
            
              _model = [[WLUserModel alloc]init];
              _model.UserId =contentUserID;
              _model.Sign = contentSign;
              
              
          }
          return _model;
      }
      
      ```

      

    ```
            UIPasteboard *pasteboardSign = [UIPasteboard pasteboardWithName:WLpasteboardWithNameKeySign create:YES];
            pasteboardSign.persistent = YES;
            pasteboardSign.string = model.Sign;
    
    ```

    ![image](https://wx3.sinaimg.cn/large/af39b376gy1fu4g5sy779j20ev05wq4a.jpg)

  * keychain

    ```
    KeychainItemWrapper class is an abstraction layer for the iPhone Keychain communication. It is merely a 
        simple wrapper to provide a distinct barrier between all the idiosyncracies involved with the Keychain
        CF/NS container objects.
        @interface CMPayKeychainItemWrapper : NSObject
    {
        NSMutableDictionary *keychainItemData;		// The actual keychain item data backing store.
        NSMutableDictionary *genericPasswordQuery;	// A placeholder for the generic keychain item query used to locate the item.
    }
    
    @property (nonatomic, retain) NSMutableDictionary *keychainItemData;
    @property (nonatomic, retain) NSMutableDictionary *genericPasswordQuery;
    
    // Designated initializer.
    - (id)initWithIdentifier: (NSString *)identifier accessGroup:(NSString *) accessGroup;
    - (void)setObject:(id)inObject forKey:(id)key;
    - (id)objectForKey:(id)key;
    
    // Initializes and resets the default generic keychain item data.
    - (void)resetKeychainItem;
    
    ```

    

  * property list files

    * NSUserDefaults

    ```
    kn-iPhone:/var/mobile/Library root# ls -lrt *Configuration*
    total 40
    -rw-rw-rw- 1 mobile mobile   181 Sep 11  2017 PayloadDependency.plist
    -rw-rw-rw- 1 mobile mobile   263 Jan 30  2018 PayloadManifest.plist
    -rw-rw-rw- 1 mobile mobile   181 Jan 30  2018 ProfileTruth.plist
    -rw-rw-rw- 1 mobile mobile   181 Aug  9 14:40 ClientTruth.plist
    -rw-r--r-- 1 mobile mobile 17729 Aug  9 19:47 EffectiveUserSettings.plist
    -rw-rw-rw- 1 mobile mobile   181 Aug  9 19:54 UserSettings.plist
    drwxr-xr-x 6 mobile mobile   192 Aug  9 19:54 PublicInfo
    
    ```

    

  

  * SQLite

    ```
    kn-iPhone:/var/mobile/Library/Accounts root# ls -lrt
    total 2236
    -rw-r--r-- 1 mobile mobile  143360 Aug  9 22:25 Accounts3.sqlite
    -rw-r--r-- 1 mobile mobile   32768 Aug  9 22:25 Accounts3.sqlite-shm
    -rw-r--r-- 1 mobile mobile 1145392 Aug  9 22:37 Accounts3.sqlite-wal
    
    ```

    

 

# Stored Data Protection

  

  

 

- All app data is encrypted at rest (using a file system key)
- When devices is turned on, then data is unlocked (decrypted)
- File access permissions:
  - No protection
  - Complete protection - the file in inaccessible when device is locked
  - Complete unless open - the file in accessible if application has it open when the device is locked
  - Complete until first user authentication - the file is always accessible after the first unlock. (This is the default setting)

 

- -  

 

 

 

 

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost ethical-hacking-mobile-devices-and-platforms Application data storage；Stored Data Protection；Cached and temp data；URL handlers；Binary protection；iOS Security -t security
> #原来""的参数，需要自己加上""
```

