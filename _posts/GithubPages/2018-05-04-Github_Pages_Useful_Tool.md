---
layout: post
title: Github_Pages_Useful_Tool
date: 2018-05-04
tag: GithubPages
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 维护GitHuh_Pages和jekyll搭建的个人博客，我写文章常用的辅助工具：ImageOptim、ipic、MacDown
---

# 前言

>* 我写文章常用的辅助工具：`ImageOptim`、`ipic`、`MacDown`
>```
> 1） ImageOptim： 用于压缩图片
> 2） ipic ：上传图片到图床
> 3） MacDown：markdown的编辑工具，支持预览
> 4)  gist.github.com: 提高代码的阅读行性，以及提升markdown的文章的精简性。
>```
>

# [解析域名： `iosre.club` ->`https://kunnan.github.io/`](https://console.cloud.tencent.com/developer)


####  添加解析
>* 管理控制台 → 域名与网站（万网） → 域名
>```
>选择你注册好的域名，点击解析
>```
>* 添加解析
>```
>记录值就是我们博客的IP地址，是 GitHub Pagas 在美国的服务器的地址 
>devzkndeMacBook-Pro:kunnan.github.io.git devzkn$ ping kunnan.github.io
PING sni.github.map.fastly.net (185.199.111.153): 56 data bytes
>```
>
>* 要解析 www.iosre.club，请填写 www。主机记录就是域名前缀，常见用法有：
>```
1) 要解析 www.iosre.club，请填写 www。
>2)要解析 iosre.club，请填写 @。
>3)* 
>4)mail
>5) 二级域名
>6）手机网站：m
>```

#### 修改CNAME

>* 然后 GitHub Pages 再通过 CNAME记录 跳转到你的主页上。
>```
>修改 我们github仓库下的 CNAME 文件
>在CNAME输入你自己的域名，就可以解析到你的主页了
>```



# ImageOptim

>* [ImageOptim/ImageOptim](https://github.com/ImageOptim/ImageOptim/blob/master/imageoptim/ImageOptimController.m#L324-L326)
>* /Users/devzkn/Downloads/kevinsoftware/ImageOptim.app/Contents/MacOS/ImageOptim
>```
>devzkndeMacBook-Pro:MacOS devzkn$ ImageOptim
2018-05-04 16:43:01.853 ImageOptim[3051:2147753] Results cache is in /Users/devzkn/Library/Caches/net.pornel.ImageOptim/Results.db
>```
>

# macdown

>* macdown 的默认打开路径
>![](https://ws2.sinaimg.cn/large/006tKfTcly1fqz2gtklehj30b20bvwf1.jpg)

>* Macdown的命令行工具
>![](https://ws4.sinaimg.cn/large/006tKfTcly1fqz2gtfh7zj309e06tt8w.jpg)
![](https://ws4.sinaimg.cn/large/006tKfTcly1fqz2gt8v1tj309w06z3yq.jpg)
>```
>devzkndeMacBook-Pro:kunnan.github.io.git devzkn$ macdown --help
usage: macdown [file ...]
Options:
-v --version Print the version and exit. 
-h --help    Print this help message and exit. 
>```
>

>* [macdown 打开文档的方式](https://gist.github.com/zhangkn/7357c847faa86741cb49d136420b2f37)
><script src="https://gist.github.com/zhangkn/7357c847faa86741cb49d136420b2f37.js"></script>



#### 扩张语法

>* 代码块和语法高亮: 
>```js
window.addEventListener('load', function() {
  console.log('window loaded');
});
>```


>* [code-blocks-and-highlighting](http://itmyhome.com/markdown/article/extension/code-blocks-and-highlighting.html)



# ipic

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqz049dx01j30wy08yt9q.jpg)

>* 默认支持markdown的图片格式，默认上传图片到新浪图床

#  [gist.github.com]( https://gist.github.com/zhangkn/)

利用gist.github.com 提高代码的阅读行性，以及提升markdown的文章的精简性。


# ppt 的操作技巧

>* 编辑幻灯片的母版来更改演示稿的设计（水印）
>```
>视图->母版->幻灯片母版。
>```
 
# [小程序发布流程](https://mp.weixin.qq.com/wxopen/initprofile?action=home&lang=zh_CN)



#### 一步步搭建小程序

>* 域名及证书配置	
>```
>购买域名 >
网站备案 >
部署SSL证书 >
>```


#### 小程序开发与管理


###### 小程序开发与管理

>* [ 普通小程序开发者工具](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/devtools.html)
>* [小游戏开发者工具](https://mp.weixin.qq.com/debug/wxagame/dev/devtools/devtools.html)

###### 添加开发者


###### 配置服务器

在[开发设置]页面查看AppID和AppSecret，配置服务器域名

###### 帮助文档

>* [普通小程序](https://mp.weixin.qq.com/debug/wxadoc/introduction/index.html)
>* [小游戏](https://mp.weixin.qq.com/debug/wxagame/introduction/index.html)
>

#### 版本发布

 先提交代码，然后提交审核，审核通过后可发布




# [开启公众号开发者模式](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1472017492_58YV5)

#### 1.1 申请服务器

>* 以腾讯云服务器为示例：[腾讯云服务器](https://buy.qcloud.com/cvm?cpu=1&mem=1)购买入口，购买指导请参考[快速入门linux云服务器](http://www.qcloud.com/doc/product/213/%E6%AD%A5%E9%AA%A4%E4%B8%80%EF%BC%9A%E6%B3%A8%E5%86%8C%E8%85%BE%E8%AE%AF%E4%BA%91%E5%B8%90%E5%8F%)

>* 学生党注意：腾讯公司为在读高校生提供了[云+校园计口](http://bbs.qcloud.com/forum.php?mod=viewthread&tid=21113&highlight=%E6%A0%A1%E5%9B%AD)，1元/月即可使用腾讯云。




# Viscosity 

是一款运行在macOS平台上的网络保护软件，使用它可以进行内部网络的连接。

# [software/confluence](https://www.atlassian.com/software/confluence)

>* 通常的层级结构是：空间->文章（技术、接口文档wiki）
>![](https://ws3.sinaimg.cn/large/006tKfTcgy1fr2mzcisttj31980e2myb.jpg)
![](https://ws1.sinaimg.cn/large/006tKfTcgy1fr2mzc78s5j30n206edge.jpg)

>* 如果新增个问答模块就更完美了


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Github_Pages_Useful_Tool 维护GitHuh_Pages和jekyll搭建的个人博客，我写文章常用的辅助工具：ImageOptim、ipic、MacDown -t GithubPages
> #原来""的参数，需要自己加上""
```

