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
> 5）rss推送功能
>```
>


# I、rss推送功能


![image](http://ws2.sinaimg.cn/large/006tBeITgy1fre8jtlgj6j30ku112diq.jpg)
![image](http://ws2.sinaimg.cn/large/006tBeITgy1fre8judyzwj30ku112jss.jpg)

>* 本站的rss: https://kunnan.github.io/feed.xml
>* 旧站的rss: https://zhangkn.github.io/feed.xml
>



# II、 [解析域名： `iosre.club` ->`https://kunnan.github.io/`](https://console.cloud.tencent.com/developer)

#### 常用的选择：GitHub Pages

>* 优点
>```
自带域名可 https 访问
可配置自定义域名
```
>* 缺点
>```
>无法给自定义域名配置 SSL,借助其他平台(cloudflare CDN)。
>不如自己买个 vps 搭建。
```



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
devzkndeMacBook-Pro:kunnan.github.io.git devzkn$ dig kunnan.github.io
; <<>> DiG 9.9.7-P3 <<>> kunnan.github.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45945
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 0
;; QUESTION SECTION:
;kunnan.github.io.		IN	A
;; ANSWER SECTION:
kunnan.github.io.	3600	IN	CNAME	sni.github.map.fastly.net.
sni.github.map.fastly.net. 1300	IN	A	185.199.110.153
sni.github.map.fastly.net. 1300	IN	A	185.199.108.153
sni.github.map.fastly.net. 1300	IN	A	185.199.111.153
sni.github.map.fastly.net. 1300	IN	A	185.199.109.153
;; Query time: 218 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Wed May 09 10:35:36 CST 2018
;; MSG SIZE  rcvd: 137
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
>
>* 域名解析
>```
>解析生效后，可以尝试访问来查看是否解析成功，直接访问 http://iosre.club，如果是 Github Pages 的 404 界面，说明解析成功了。
>```
>


#### 修改CNAME

>* 然后 GitHub Pages 再通过 CNAME记录 跳转到你的主页上。
>```
>修改 我们github仓库下的 CNAME 文件
>在CNAME输入你自己的域名，就可以解析到你的主页了
>```

>* https://github.com/kunnan/kunnan.github.io/settings
>```
>Your site is ready to be published at https://kunnan.github.io/. 修改之前的情况
> Your site is published at http://iosre.club/修改之后的情况
>```
>
>* Enforce HTTPS
>```
>Enforce HTTPS — Unavailable for your site because your domain is not properly configured to support HTTPS (iosre.club) 
1) HTTPS provides a layer of encryption that prevents others from snooping on or tampering with traffic to your site.
When HTTPS is enforced, your site will only be served over HTTPS. Learn more.
>```
>
>* [securing-your-github-pages-site-with-https](https://help.github.com/articles/securing-your-github-pages-site-with-https)
>


####  [部署SSL证书](https://console.cloud.tencent.com/ssl)

>* 证书申请
>```
>您的域名已使用云解析服务，可自动添加DNS记录验证，无需您进行任何操作
>您的申请信息已提交。腾讯云将在一个工作日内完成审核，审核结果将以短信、邮件及站内信的方式通知您。
>您的证书已颁发，可下载到本地。证书安装方法可参考指引文档
>```
>* [证书安装指引](https://cloud.tencent.com/document/product/400/4143)
>* [自定义域名的 GitHub Pages 添加 SSL 的方案:利用 Cloudflare 反代实现全站 HTTPS](https://blog.itswincer.com/posts/444a2b9d/)
>```
>cloudflare 免费版并不是很快。而且他强制你 nameserver 指过去。反正我不想用
>```
>* [介绍一些免费好用的静态网站托管服务 #55](https://github.com/lmk123/blog/issues/55)
>```
> 1) GitLab Pages:同样跟 GitHub Pages 的功能一样，但是：自定义域名可配置 https，不过需要上传证书
> https://docs.gitlab.com/ee/user/project/pages/index.html
> 2)Netlify（推荐）https://www.netlify.com/  
> --可以使用 CLI 上传代码
> --支持自定义域名且自定义域名支持一键开启 https（证书来自 Let's Encrype）
> --支持强制让用户通过 https 访问网站（开启后此功能后，http 的访问一律会 301跳转到 https
> --支持自动构建
> --支持重定向（Redirects）和重写（Rewrites）功能
> --数据通过 HTTP2 协议传输
> --提供 webhooks 与 API
> Netlify 有个问题是会自动把静态资源上传到 cloudfront CDN，但国内有些地方访问 cloudfront 速度很慢或部分被墙。
>```
>* [在GitHub Pages上使用CloudFlare(简称CF) https CDN](https://blog.chionlab.moe/2016/01/28/github-pages-with-https/)
>```
>一家CDN提供商，它的free plan里面就提供https服务（免费计划不能上传SSL）。
>现在可以通过CF实现：从用户到CDN服务器的连接为https，而CDN服务器到GitHub Pages服务器的连接为http。
>当用户通过该域名访问CF的CDN时（仅限http或https），CDN再转发到刚才填写的真实目的主机（即username.github.io）
>```
>
>



####  [小程序配置指引、升级方案](https://github.com/tencentyun/weapp-doc)



# III、在网页或博客中嵌入演示文稿

#### 使用Google文档、表格和幻灯片来查看和编辑Microsoft Word、Excel和PowerPoint文件

>* [欢迎使用Google幻灯片](https://docs.google.com/presentation/u/0/)

```
快速创建新演示文稿或打开现有演示文稿。与他人共享即可协同编辑。
```
>* [Google文档、表格及幻灯片的Office编辑：chrome 用户需要安装的插件](http://goo.gl/VZm5xc)
>

>* [Google文档](https://docs.google.com/document/u/0/)
>
>* [pdf 课件](https://drive.google.com/file/d/1lld_y7puHyRfyuYAMnDaS3hAb6mzr2tn/view)

#### 推荐使用onedrive

>* [ppt课件](https://1drv.ms/p/s!AgrZWf10_6KoabJojRKrceecyy8)

>* [onedrive.live.com](https://onedrive.live.com/?id=root&cid=A8A2FF74FD59D90A)
>
>*  [PowerPoint.aspx: 上传ppt,在网页或博客中嵌入演示文稿](https://office.live.com/start/PowerPoint.aspx?s=2&auth=1&nf=1&ppud=4)

>* [在网页或博客中嵌入演示文稿](https://support.office.com/zh-cn/article/%E5%9C%A8%E7%BD%91%E9%A1%B5%E6%88%96%E5%8D%9A%E5%AE%A2%E4%B8%AD%E5%B5%8C%E5%85%A5%E6%BC%94%E7%A4%BA%E6%96%87%E7%A8%BF-19668a1d-2299-4af3-91e1-ae57af723a60)


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

>* 代码块和语法高亮: `swift`、`js`、`objc`
>```js
window.addEventListener('load', function() {
  console.log('window loaded');
});
>```
> <script src="https://gist.github.com/zhangkn/9cb1b6fa6485f6df0cdd3441b103746b.js"></script>
> 

>* [code-blocks-and-highlighting](http://itmyhome.com/markdown/article/extension/code-blocks-and-highlighting.html)



# ipic

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqz049dx01j30wy08yt9q.jpg)

>* 默认支持markdown的图片格式，默认上传图片到新浪图床
>```
>https://chrome.google.com/webstore/detail/%E6%96%B0%E6%B5%AA%E5%BE%AE%E5%8D%9A%E5%9B%BE%E5%BA%8A/fdfdnfpdplfbbnemmmoklbfjbhecpnhf
>```
>* [weibo_util.py](https://github.com/noteblogpost/hexo_weibo_image)
>```
>/usr/bin/python --version Python 2.7.10
> python --version Python 3.6.5
> ImportError: No module named requests
devzkndeMacBook-Pro:hexo_weibo_image devzkn$ sudo pip install requests
ImportError: No module named rsa
devzkndeMacBook-Pro:hexo_weibo_image devzkn$ sudo pip install rsa
>```
>* [minipublish](https://weibo.com/minipublish)
>* [目前我使用的工具Weibo-Picture-Store](https://github.com/noteblogpost/Weibo-Picture-Store)



#  [gist.github.com]( https://gist.github.com/zhangkn/)

利用gist.github.com 提高代码的阅读行性，以及提升markdown的文章的精简性。


# ppt 的操作技巧

>* 编辑幻灯片的母版来更改演示稿的设计（水印）
>```
>视图->母版->幻灯片母版。
>```
 
# [小程序发布流程](https://mp.weixin.qq.com/wxopen/initprofile?action=home&lang=zh_CN)



#### [一步步搭建小程序](https://cloud.tencent.com/act/weapp/package?fromSource=gwzcw.916481.916481.916481)

>* 域名及证书配置	
>```
>购买域名 >
网站备案 >
部署SSL证书 > https://console.cloud.tencent.com/ssl
>```


#### 小程序开发与管理

>*
>```
>devzkndeMacBook-Pro:iosre devzkn$ git init
>git add .
> git commit -m "demo"
>```

###### 小程序开发与管理

>* [ 普通小程序开发者工具](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/devtools.html)
>* [小游戏开发者工具](https://mp.weixin.qq.com/debug/wxagame/dev/devtools/devtools.html)

###### 添加开发者


###### 配置服务器

在[开发设置]页面查看AppID和AppSecret，配置服务器域名

###### 帮助文档

>* [普通小程序](https://mp.weixin.qq.com/debug/wxadoc/introduction/index.html)
>* [小游戏](https://mp.weixin.qq.com/debug/wxagame/introduction/index.html)
>* [API](https://developers.weixin.qq.com/miniprogram/dev/api/)
>* [开发支持](https://mp.weixin.qq.com/cgi-bin/wx)

#### 版本发布

>*  先提交代码，然后提交审核，审核通过后可发布
>```
>多次提交测试内容或 Demo，将受到相应处罚。
>```



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



# Google AdSense

>* Google AdSense 帐户已被停用
>```
>尊敬的发布商：
您好！我们的广告计划致力于为发布商、广告客户和用户营造一个“三赢”的在线广告生态系统。为此，一旦发现帐户对用户或广告客户的行为可能会对此生态系统的形象带来负面影响，我们就必须对这些帐户采取措施。就您的情况而言，我们在您的AdSense帐户中发现了无效活动，因此已停用您的帐户。
由于受到限制，我们无法向您提供过多的违规相关信息。我们理解您可能希望获得有关帐户活动的更多信息。不过为了保护我们专有的检测系统，我们无法提供进一步的详情。
在某些情况下，发布商可以通过一些重大调整来修正违规问题，他们也愿意遵守AdSense合作规范。因此，我们设置了申诉流程，并希望借此协助您解决问题。请务必针对您的流量提供全面分析，或详细说明可能导致了您的申诉所涉无效活动的原因。请使用此表单提交申诉，我们将进行相应的跟进。在提交申诉之前，请参阅帐户停用的最常见原因列表。
感谢您的理解。
此致
Google AdSense小组敬上
>```

# See Also 
>* [maxfong.github.com: 可以考虑的新博客模版](https://github.com/kunnan/maxfong.github.com)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Github_Pages_Useful_Tool 维护GitHuh_Pages和jekyll搭建的个人博客，我写文章常用的辅助工具：ImageOptim、ipic、MacDown -t GithubPages
> #原来""的参数，需要自己加上""
```

