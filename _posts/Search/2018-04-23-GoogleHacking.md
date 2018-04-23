---
layout: post
title: GoogleHacking
date: 2018-04-23
tag: Search
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Advanced Google Dorking Commands
---

# 前言

>* [Google hacking, also known as Google Dorking](https://en.wikipedia.org/wiki/Google_hacking)
>```
is a computer hacking technique that uses Google Search and other Google applications to find security holes in the configuration and computer code that websites use.
```

>* the basic syntax for using the advanced operator in Google is as follows:
>```
>Operator_name:keyword
>allintext:Google dorking
>```


# 正文
you can use Google to find the vulnerable database, websites, security cameras and other Internet of Things connected devices.

#### Simple Google Dorks:

>* Allintext
>```
>Searches for occurrences of all the keywords given
>```
>* Intext	
>```
>Searches for the occurrences of keywords all at once or one at a time
```
>* Inurl	
>```
>Searches for a URL matching one of the keywords
>```
>* Allinurl
>```
>Searches for a URL matching all the keywords in the query
```
>* Intitle	
>```
>Searches for occurrences of keywords in URL all or one
>```
>* Allintitle
>```
>	Searches for occurrences of keywords all at a time
>```
>* Site
>```
>	Specifically searches that particular site and lists all the results for that site
>```
>* filetype
>```
>	Searches for a particular filetype mentioned in the query
>```
>* Link	
>```
>Searches for external links to pages
>The "link:" search operator that Google used to have, has been turned off by now (2017)
>```
>* Numrange
>```
>	Used to locate specific numbers in your searches
>```
>* Daterange
>```
>	Used to search within a particular date range
>```



#### Advanced operators

>* [cache:https://zhangkn.github.io intitle:ReverseEngineering intext:ios](http://webcache.googleusercontent.com/search?q=cache:https://zhangkn.github.io++intitle:ReverseEngineering+intext:ios&num=1&safe=strict&biw=1280&bih=656&strip=1&vwsrc=0)
```
This is Google's cache of https://zhangkn.github.io/. It is a snapshot of the page as it appeared on 20 Feb 2018 15:37:02 GMT.
输入URL，搜索特定页面的缓存快照，即使目标页面发生变动甚至不存在了，依然可以看到它的副本
```

>* define:ReverseEngineering
```
搜索输入关键词或关键词组的定义来源链接;该操作符不能与其他操作符及关键字混用。
```

>* info:https://zhangkn.github.io
```
搜索输入URL的摘要信息和其他相关信息，该操作符不能与其他操作符及关键字混用
```
>* related:https:zhangkn.github.io
```
冒号后接一个URL，搜索与该URL相关的页面
```

>* stocks:高阳
```
搜索关于指定公司的股票市场信息
stocks:中国移动
```


#### Basics: some examples of using Google Dorking

>* inurl:group_concat(username, filetype:php intext:admin
>```
>Using the above information, we were able to tap in to some of the SQL injection results done by somebody else on the sites.
>```
>* intext:@gmail.com filetype:xls
>```
>be used to glean emails ids from Google.
>```
>* site:github.com -site:www.github.com -site:bitcoin.github.com  -site:https://github.com/zhangkn
>
>* inurl:8443 -intext:8443
>```
> 端口扫描
> inurl:8080 -intext:8080
> inurl:nqt -intext:8080 filetype:php intext:"network query tool"  --http://portal.trgsites.de/network/nqt.php
>```
>* filetype:log inurl password login
>```
>SQL数据库挖掘:
>filetype:sql + "IDENTIFIED BY" -cvs
>filetype:sql + "IDENTIFIED BY" ("Grant * on *" | "create user")
>filetype:mp4 inurl:xxx intext:xxx: 在渗透检测过程中省时省力
>```
>* "#-Frontpage-" inurl:administrators.pwd
>* inurl:"ViewerFrame?Mode="
>```
>find public web cameras
>```
>* intitle:index.of
>```
>This can give a list of files on the servers
>```
>
>* intitle:index.of mp3
>```
>will give all the MP3 files available on various servers
>```
>


# see also 

>* [Advanced operators](https://en.wikipedia.org/wiki/Google_hacking)
<p>There are many similar advanced operators which can be used to exploit insecure websites:</p>
<table class="wikitable">
<tr>
<th>Operator</th>
<th>Purpose</th>
<th>Mixes with Other Operators?</th>
<th>Can be used Alone?</th>
<th>Web</th>
<th>Images</th>
<th>Groups</th>
<th>News</th>
</tr>
<tr>
<td>intitle</td>
<td>Search page Title</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
</tr>
<tr>
<td>allintitle<sup id="simple-google-dorks" class="reference"><a href="#simple-google-dorks">[simple-google-dorks]</a></sup></td>
<td>Search page title</td>
<td>no</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
</tr>
<tr>
<td>inurl</td>
<td>Search URL</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>not really</td>
<td>like intitle</td>
</tr>
<tr>
<td>allinurl</td>
<td>Search URL</td>
<td>no</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>like intitle</td>
</tr>
<tr>
<td>filetype</td>
<td>specific files</td>
<td>yes</td>
<td>no</td>
<td>yes</td>
<td>yes</td>
<td>no</td>
<td>not really</td>
</tr>
<tr>
<td>intext</td>
<td>Search text of page only</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
</tr>
<tr>
<td>allintext</td>
<td>Search text of page only</td>
<td>not really</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
</tr>
<tr>
<td>site</td>
<td>Search specific site</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>no</td>
<td>not really</td>
</tr>
<tr>
<td>link</td>
<td>Search for links to pages</td>
<td>no</td>
<td>yes</td>
<td>yes</td>
<td>no</td>
<td>no</td>
<td>not really</td>
</tr>
<tr>
<td>inanchor</td>
<td>Search link anchor text</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>not really</td>
<td>yes</td>
</tr>
<tr>
<td>numrange</td>
<td>Locate number</td>
<td>yes</td>
<td>yes</td>
<td>yes</td>
<td>no</td>
<td>no</td>
<td>not really</td>
</tr>
<tr>
<td>daterange</td>
<td>Search in date range</td>
<td>yes</td>
<td>no</td>
<td>yes</td>
<td>not really</td>
<td>not really</td>
<td>not really</td>
</tr>
<tr>
<td>author</td>
<td>Group author search</td>
<td>yes</td>
<td>yes</td>
<td>no</td>
<td>no</td>
<td>yes</td>
<td>not really</td>
</tr>
<tr>
<td>group</td>
<td>Group name search</td>
<td>not really</td>
<td>yes</td>
<td>no</td>
<td>no</td>
<td>yes</td>
<td>not really</td>
</tr>
<tr>
<td>insubject</td>
<td>Group subject search</td>
<td>yes</td>
<td>yes</td>
<td>like intitle</td>
<td>like intitle</td>
<td>yes</td>
<td>like intitle</td>
</tr>
<tr>
<td>msgid</td>
<td>Group msgid search</td>
<td>no</td>
<td>yes</td>
<td>not really</td>
<td>not really</td>
<td>yes</td>
<td>not really</td>
</tr>
</table>

>* [advanced-google-dorking-commands](https://www.cybrary.it/0p3n/advanced-google-dorking-commands/)
>
>```
>allintext:Google dorking
>
> `some advanced commands`:
> site:static.ow.ly/docs/ intext:@gmail.com | Password
> 
> filetype:sql intext:wp_users phpmyadmin
> 
> intext:”Dumping data for table `orders`”
> 
> “Index of /wp-content/uploads/backupbuddy_backups” zip
> 
> Zixmail inurl:/s/login?
> 
> inurl:/remote/login/ intext:”please login”
> 
> intext:”FortiToken clock drift detected”
> 
> inurl:/WebInterface/login.html
> 
> inurl:dynamic.php?page=mailbox
> 
> inurl:/sap/bc/webdynpro/sap/ | “sap-system-login-oninputprocessing”
> 
> intext:”Powered by net2ftp”
>```

>* [google-hacking-database](https://www.exploit-db.com/google-hacking-database/)
>
>```
>Date	Title	Category
2018-04-18	inurl:default.aspx?ReturnUrl=/spssmr -stackoverflow -youtube.com -github	Pages Containing Login Portals
2018-04-18	inurl:"/SAMLLogin/" -github	Pages Containing Login Portals
2018-04-17	Drupal CMS - Drupalgeddon2	Vulnerable Servers
2018-04-17	intext:build:SVNTag= JBoss intitle:Administration Console inurl:web-console	Various Online Devices
2018-04-17	Codeigniter filetype:sql intext:password | pwd intext:username | uname intext: Insert into users values	Files Containing Passwords
2018-04-17	"login" "adp login" -adplogin.us -adplogin.org -adplogin.net	Pages Containing Login Portals
2018-04-16	intitle:"index.of" | inurl:/filemanager/connectors/ intext:uploadtest.html	Sensitive Directories
2018-04-16	intitle:\index.of inurl:/websendmail/	Sensitive Directories
2018-04-16	:DIR | intitle:index of inurl://whatsapp/	Sensitive Directories
2018-04-16	inurl:report.cgi?dashboard=	Various Online Devices
>```
>* [GoogleHacking](https://zhangkn.github.io/2018/02/GoogleHacking/)
>* [githubSearch](https://zhangkn.github.io/2018/04/githubSearch/)
>* [Shodan is the world's first search engine for Internet-connected devices.](https://www.shodan.io/)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
```
/Users/devzkn/bin/knpost GoogleHacking Advanced Google Dorking Commands -t Search
> ps:需要自己加上"GoogleHacking Advanced Google Dorking Commands"
```