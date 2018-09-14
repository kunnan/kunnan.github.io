---
layout: post
title: writeVideoAtPathToSavedPhotosAlbum
date: 2018-09-14
tag: Video
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 使用dataWithContentsOfURL进行视频下载，并保存到相册；以便提供给其他app进行加载使用
---



# code

> * code 
>
>   ```objc
>           NSFileManager *fileManage = [NSFileManager defaultManager];
>           NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:videoUrl]];
>           
>           if (data == nil)
>           {
>               NSLog(@"网络出错,请稍后再试");
>           }
>           else
>           {
>               //用单例类  NSFileManager的对象,将文件写入本地
>               BOOL isSuccess = [fileManage createFileAtPath:path contents:data attributes:nil];
>               if (isSuccess)
>               {
>                   NSLog(@"视频下载成功");
>                   // 保存视频到相册
>                   ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
>                   [library writeVideoAtPathToSavedPhotosAlbum:[NSURL fileURLWithPath:path]
>                    
>                                               completionBlock:^(NSURL *assetURL, NSError *error) {
>                                                   
>                                                   if (error) {
>                                                       
>                                                       NSLog(@"Save video fail:%@",error);
>                                                       
>                                                   } else {
>                                                       //2018-09-13 20:03:33.870 WeChat[6484:1077151] [MMVideoCompressHelper getCacheFilePathFrom:file:///var/mobile/Media/DCIM/100APPLE/IMG_0041.mp4 ]
>                                                       NSLog(@"Save video succeed.:%@",assetURL);//assets-library://asset/asset.mp4?id=45C3D675-C625-4C52-B133-66D0A709AC57&ext=mp4
>                                                       
>                                                       // 获取相册的最新一条视频的path，进行SightDraft的创建
>                                                       
>                                                   }
>                                                   
>                                               }];
>               }
>               else
>               {
>                   NSLog(@"视频下载失败");
>               }
>   
>   ```
>

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost writeVideoAtPathToSavedPhotosAlbum 使用dataWithContentsOfURL进行视频下载，并保存到相册；以便提供给其他app进行加载使用 -t Video
> #原来""的参数，需要自己加上""
```

