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


# 1、iOS applications
####  I 、iOS applications

 

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

   

   

  # 2、Application data storage

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

    

 

#### I、Stored Data Protection

  

  

 

- All app data is encrypted at rest (using a file system key)

- When devices is turned on, then data is unlocked (decrypted)

- File access permissions:

  - No protection
  - Complete protection - the file in inaccessible when device is locked
  - Complete unless open - the file in accessible if application has it open when the device is locked
  - Complete until first user authentication - the file is always accessible after the first unlock. (This is the default setting)

- Key Chain - has multiple security options, like:

  - Write only if passcode is set
  - Read on this device only

   

  

  

 

 

#### II 、Cached and temp data

iOS stores an unencrypted screen shot of the app when it goes to the background which can be used to recover any sensitive information that is visible on that screen.

 

> * Request & Response data is stored in SQLite DB called `cached.db`.
>
>   ```
>       const char* path = "/private/var/mobile/Library/MusicLibrary/AccountCache.sqlitedb";
>           const char* path = "/private/var/mobile/Library/Accounts/Accounts3.sqlite";
>               const char* path = "/private/var/mobile/Library/com.apple.itunesstored/kvs.sqlitedb";
>   ```
>
>   

  

 

 #### III 、URL handlers

 

Some apps might use URL handlers to pass sensitive information between processes (they will be listed in `Info.plist` under `URLTypes` section)



> * pass sensitive information between processes
>
>   * [processCommunicationByRrocketbootstrap](https://zhangkn.github.io/2018/01/Inter-processCommunicationByRrocketbootstrap/)
>
>   *  URL handlers
>
>      

#### IV 、Binary protection

 Many apps uses extra flags to secure the app binary, like: Data Execution Prevention (`DEP`), `Postion Independent Executable` & `ASLR`.
It’s good to check for those flags.

> * ASLR 
>   * [removePIE](https://github.com/zhangkn/removePIE)
>   * [toggle-pie changes the MH_PIE flag of the MACH-O header on iOS applications to disable ASLR on applications. Works on iOS 8 and 32/64-bit binaries (including FAT binaries).     ](https://github.com/zhangkn/KNtoggle-pie)
> * [有关iOS安全加固的几种方法，代码混淆，类名方法名混淆等攻防技术](https://github.com/zhangkn/KYSecurityDefense)



# 3、iOS Security

> * This process provides a secure way of loading the System.
>
>    

```
   --------        ---------------       ---------       ---------- 
   | Boot |        | Low level   |       | iBoot |       | Kernel |
   | rom  |  -->   | Boot loader |  -->  | load  |  -->  | Load   |
   --------        ---------------       ---------       ----------
```

 

- `Boot rom hardware` contains `RO` code to bootstrap the system and Apple’s public key. 

- Public key is used to verify the integrity of the `Low level boot loader` code
- `Low level boot loader` takes the 2nd stage boot called `iBoot load` code from flash memory and verifies its signature before loading.
- `iBoot load` verifies the Kernel code in similar fashion before loading it.

 

#### I、 Application Sandboxing

- all apps run in sandboxes, but not all apps use all security features provided by the iOS
- Interactions outside the sandbox have to be explicit. For example accessing microphone, camera, media etc.

 

#### II、Exploitation protection



- Memory pages can be marked as “Write but not execute” (using ARM chip `W^X`feature, similar to `DEP` for Windows users)
- `ASLR` - makes sure that code & data are not stored together, so it makes exploitaion of fixed memory addresses more difficult
- `Position-independent execution` (`PIE`) - allows application to work from any location in memory
- `Stack canaries` - can be used to check for malicious or accidental stack overwrites. A function can always check whether “`canary`” is stil alive.

 

#### III、Interesting folders on iOS devices



###### 1)/var/mobile 

```
iPhone:/var/mobile root# ls -rlt
total 0
drwxr-xr-x  5 mobile mobile  170 Dec 28  2014 Containers/
drwxr-xr-x  2 mobile mobile  102 Dec 28  2014 MobileSoftwareUpdate/
drwx------  2 mobile mobile   68 Dec 28  2014 Downloads/
drwxr-xr-x  3 mobile mobile  102 Dec 28  2014 Documents/
drwx--x--x 63 mobile mobile 2210 Dec 23  2017 Library/
drwxr-xr-x  2 mobile mobile   68 May 11 18:25 nztdata/
drwxr-xr-x  2 mobile mobile   68 May 11 18:25 importdata/
drwxr-xr-x  2 mobile mobile   68 May 11 18:25 iggdata/
drwxr-xr-x  2 mobile mobile   68 May 11 18:25 exportdata/
drwxr-x--- 18 mobile mobile  918 Aug  2 17:04 Media/

```



- /var/mobile 

  

  * `/var/mobile/Library/SMS `

    - contains files like `sms.db` - with all SMS msgs in clear text

      ```
      -rw-r--r-- 1 mobile mobile   4096 Dec 28  2014 sms.db
      drwxr-xr-x 3 mobile mobile    102 Dec 27  2017 Drafts/
      drwxr-xr-x 2 mobile mobile    102 Aug  6 16:45 EmergencyAlerts/
      -rw-r--r-- 1 mobile mobile 733392 Aug  8 08:52 sms.db-wal
      -rw-r--r-- 1 mobile mobile  32768 Aug  8 08:52 sms.db-shm
      ```

      

     

  * `/var/mobile/Library/Accounts` :contains files like Accounts3.sqlite

    * `wal` is a temporary write-ahead logging file
    * `shm` is an index file for `wal`

    

    ```
    iPhone:/var/mobile root# ls -lrt /var/mobile/Library/Accounts
    total 328
    -rw-r--r-- 1 mobile mobile  98304 Dec 28  2014 Accounts3.sqlite
    -rw-r--r-- 1 mobile mobile 201912 Jun  4 21:26 Accounts3.sqlite-wal
    -rw-r--r-- 1 mobile mobile  32768 Aug  3 14:01 Accounts3.sqlite-shm
    
    ```

    

  * `/var/mobile/Media`  : contains books, photos etc

    * Recordings 

      

      ```
      iPhone:/var/mobile/Media root# ls -rlt Recordings
      total 60
      -rw-r--r-- 1 mobile mobile     0 Jul  4 16:41 Recordings.db-wal
      -rw-r--r-- 1 mobile mobile 28672 Jul  4 16:41 Recordings.db
      -rw-r--r-- 1 mobile mobile 32768 Aug  3 15:17 Recordings.db-shm
      
      ```

      

    ```
    drwxr-xr-x  5 mobile mobile      238 Jan 18  2018 Books/
    drwxr-xr-x 11 mobile mobile      782 Jul 26 15:28 PhotoData/
    drwxr-xr-x  2 mobile mobile      170 Apr 23  2017 Radio/
    drwxr-xr-x  4 mobile mobile      136 Apr 21  2017 Photos/
    
    ```

    

  * `/var/mobile/Applications` -> contain a bunch of UUIDs sandbox folders for each application

  * `/var/mobile/Containers` -> similar as above.
    * ls -lrt

      ```
      iPhone:/var/mobile root# ls -lrt Containers
      total 0
      drwxr-xr-x 3 mobile mobile 102 Dec 28  2014 Shared/
      drwxr-xr-x 5 mobile mobile 170 Dec 28  2014 Data/
      drwxr-xr-x 4 mobile mobile 136 Dec 28  2014 Bundle/
      ```

      

    * `iPhone:/var/mobile root# ps -e |grep WeChat `

      ```
      iPhone:/var/mobile root# ps -e |grep WeChat
       6367 ??        39:01.72 /var/mobile/Containers/Bundle/Application/0EF4E0E0-E22B-4F87-A734-8BC3A25B2E3A/weixin.app/WeChat
      ```

      

      





  

   

   

  

  ###### 2)/var/stash 

- `/var/stash`

  * lrwxr-xr-x 1 root wheel 13 Oct 28  2017 **/var/stash** -> **/var/db/stash**/ 

    * :/var/db/stash/_.TvfJPY/Applications/Cydia.app 

      

      ```
      -rw-r--r-- 1 root wheel   16223 Feb 16  2017 Default-568h\@2x.png
      -rwxr-xr-x 1 root wheel 2093808 Feb 16  2017 Cydia*
      lrwxr-xr-x 1 root wheel       5 Feb 16  2017 store -> Cydia*
      drwxr-xr-x 2 root admin     136 Apr 23  2017 zh-Hant.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 zh-Hans.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 vi.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 tr.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 th.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 sv.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 ru.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 pt.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 pt-br.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 pl.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 nl.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 ko.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 ja.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 it.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 he.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 fr.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 es.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 en.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 el.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 de.lproj/
      drwxr-xr-x 2 root admin     136 Apr 23  2017 ar.lproj/
      drwxr-xr-x 2 root admin     102 Oct 28  2017 menes/
      drwxr-xr-x 2 root admin     102 Oct 28  2017 Sources/
      drwxr-xr-x 2 root admin    1428 Oct 28  2017 Sections/
      drwxr-xr-x 2 root admin     306 Oct 28  2017 Purposes/
      iPhone:/var/db/stash/_.TvfJPY/Applications/Cydia.app root# 
      
      ```

      

    ```
    iPhone:~ root# ls -lrt /var/db/stash/
    total 28
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.TvfJPY/
    -rw-r--r-- 1 root wheel  13 Apr 23  2017 _.TvfJPY.lnk
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.E131Zh/
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.Kjwk6z/
    -rw-r--r-- 1 root wheel  18 Apr 23  2017 _.E131Zh.lnk
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.eZ1ZLT/
    -rw-r--r-- 1 root wheel  18 Apr 23  2017 _.Kjwk6z.lnk
    -rw-r--r-- 1 root wheel  12 Apr 23  2017 _.7LJr2g.lnk
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.7LJr2g/
    drwxr-xr-x 3 root admin 102 Apr 23  2017 _.wB3TqU/
    -rw-r--r-- 1 root wheel  12 Apr 23  2017 _.eZ1ZLT.lnk
    -rw-r--r-- 1 root wheel  10 Apr 23  2017 _.wB3TqU.lnk
    -rw-r--r-- 1 root wheel  22 Dec  8  2017 _.BOpzrd.lnk
    drwxr-xr-x 3 root admin 102 Dec  8  2017 _.BOpzrd/
    
    ```

    

 



#### IV、 iOS testing tools

- Once iOS device is jailbroken and you install `openssh` via `Cydia` you can change root password with `passwd`

- [`Clutch`]([Clutch](https://cydia.iphonecake.com/)) - identify all encrypted applications and reverse engineer them to allos src code analysis. It decrypts an app and dumps it into an ipa file.

  * `chmod +x /usr/bin/Clutch` - this has to be done before running
  * `Clutch -i` → list all encrypted applications

     

-  Erica - allows to view plist files in human readable mode
   * example usage: `plutil Info.plist`




- [Class-Dump-Z](https://kowalcj0.github.io/post/2018/ethical-hacking-mobile-devices-and-platforms/cydia.radar.org) -
- IPA Installer
- WinSCP - handy when exchanging files between phone and laptop
- IDA Pro
- [Snoop It](https://www.nesolabs.de/) - used for dynamic analysis of application operation.
- [hopperapp](htpps://www.hopperapp.com) - handy disassembler
- [the static analysis of iOS application](https://zhangkn.github.io/2017/01/iOS_Wifilist/)

 

###### Extracting properties and class headers



> * ipainstaller -c your app.ipa
>
>   * ```
>     replace installed encrypted app with its decrypted ipa counterpart, this is
>      useful cos we can extract properties list and class headers
>     ```
>
> * clutch
>
>   * clutch -i : list all encrypted apps
>
>   * clutch -d 8 : decrypt selected app into an ipa file (which essentially is a zip file)
>
>     



 

 

 

# See Also 

>* The OWASP blog has global announcements - [Click Here](http://owasp.blogspot.com/)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
>* [*The Open Web Application Security Project, an onlinecommunity, produces freely-available articles, methodologies, documentation, tools, andtechnologies in the field of web application security.*](https://www.owasp.org/index.php/OWASP_Guide_Project)
>
```
/Users/devzkn/bin//knpost ethical-hacking-mobile-devices-and-platforms Application data storage；Stored Data Protection；Cached and temp data；URL handlers；Binary protection；iOS Security -t security
> #原来""的参数，需要自己加上""
```

