---
layout: post
title: githubSearch
date: 2018-04-24
tag: Search
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Advanced github search options
---


# 前言



>* 1.[Search](https://github.com/search?utf8=%E2%9C%93&q=+user%3Azhangkn+user%3Aantire+repo%3Azhangkn%2Fzhangkn.github.io+repo%3Aantire%2Fzhangkn.github.io+created%3A%3E2017-01-01+created%3A2017-01-02+stars%3A0..1+stars%3A1+stars%3A%3E1+forks%3A0..1+forks%3A1+forks%3A%3C10+size%3A1+pushed%3A%3C2018-09-09+extension%3Ajpg+extension%3Ahtml+size%3A1..1000+size%3A%3E1+path%3A%2F_site+language%3ACSS%09&type=Repositories)
>```
>user:zhangkn user:antire repo:zhangkn/zhangkn.github.io repo:antire/zhangkn.github.io created:>2017-01-01 created:2017-01-02 stars:0..1 stars:1 stars:>1 forks:0..1 forks:1 forks:<10 size:1 pushed:<2018-09-09 extension:jpg extension:html size:1..1000 size:>1 path:/_site language:CSS
>```
>
>* 2.[Trending](https://github.com/trending)
>* 3.快捷键
```
>[shift + / ]
>[t]
```



#### 1. Search

>* [Repository search ](https://help.github.com/articles/searching-repositories)

```
<!-- cat stars:>100	 -->
Find cat repositories with greater than 100 stars.
<!-- user:defunkt -->
	Get all repositories from the user defunkt.
<!-- pugs pushed:>2013-01-28	 -->

Pugs repositories pushed to since Jan 28, 2013.

<!-- node.js forks:<200	 -->
Find all node.js repositories with less than 200 forks.
<!-- jquery size:1024..4089	 -->
Find jquery repositories between the sizes 1024 and 4089 kB.
<!-- gitx fork:true -->
	Repository search includes forks of gitx.
<!-- gitx fork:only -->
	Repository search returns only forks of gitx.

```

>* [Basic search]

```


<!--  cat stars:>100: -->

	Find cat repositories with greater than 100 stars.

<!--  user:defunkt	: -->

Get all repositories from the user defunkt.

<!--  tom location:"San Francisco, CA"	: -->

Find all tom users in "San Francisco, CA".

<!--  join extension:coffee -->

	Find all instances of join in code with coffee extension.


<!--  NOT cat	 -->

Excludes all results containing cat
```


>* [Advanced search](https://github.com/search?utf8=%E2%9C%93&q=user%3Azhangkn+user%3Aantire+repo%3Azhangkn%2Fzhangkn.github.io+repo%3Aantire%2Fzhangkn.github.io+created%3A%3E2017-01-01+created%3A2017-01-02+stars%3A0..1+stars%3A1+stars%3A%3E1+forks%3A0..1+forks%3A1+forks%3A%3C10+size%3A1+pushed%3A%3C2018-09-09+extension%3Ajpg+extension%3Ahtml+size%3A1..1000+size%3A%3E1+path%3A%2F_site+comments%3A0..1+comments%3A%3E0+label%3Abug+author%3Azhangkn+mentions%3Azhangkn+assignee%3Azhangkn+updated%3A%3C2018-09-09+fullname%3Azhangkn+location%3ACA+followers%3A0..1000+followers%3A%3E1+followers%3A%3C10000+repos%3A0+repos%3A%3C90+repos%3A%3E5+updated%3A%3C2018-09-09+language%3ACSS+language%3ACSS&type=Repositories&ref=advsearch&l=CSS&l=CSS)

```

user:zhangkn user:antire repo:zhangkn/zhangkn.github.io repo:antire/zhangkn.github.io created:>2017-01-01 created:2017-01-02 stars:0..1 stars:1 stars:>1 forks:0..1 forks:1 forks:<10 size:1 pushed:<2018-09-09 extension:jpg extension:html size:1..1000 size:>1 path:/_site comments:0..1 comments:>0 label:bug author:zhangkn mentions:zhangkn assignee:zhangkn updated:<2018-09-09 fullname:zhangkn location:CA followers:0..1000 followers:>1 followers:<10000 repos:0 repos:<90 repos:>5 updated:<2018-09-09 language:CSS language:CSS


```


>* [searching-code](https://help.github.com/articles/searching-code)

```
<!-- install repo:charles/privaterepo -->

	Find all instances of install in code from the repository charles/privaterepo.
<!-- shogun user:heroku	 -->

Find references to shogun from all public heroku repositories.
<!-- join extension:coffee	 -->
Find all instances of join in code with coffee extension.
<!-- system size:>1000	 -->
Find all instances of system in code of file size greater than 1000kbs.
<!-- examples path:/docs/ -->
	Find all examples in the path /docs/.
<!-- replace fork:true -->
	Search replace in the source code of forks.
```

- [Issue search ](https://help.github.com/articles/searching-issues)

```
<!-- encoding user:heroku -->
	Encoding issues across the Heroku organization.
<!-- cat is:open	 -->
Find cat issues that are open.
<!-- strange comments:>42	 -->

Issues with more than 42 comments.

<!-- hard label:bug -->
	Hard issues labeled as a bug.

<!-- author:mojombo -->
	All issues authored by mojombo.
<!-- mentions:tpope	 -->
All issues mentioning tpope.
<!-- assignee:rtomayko	 -->
All issues assigned to rtomayko.
<!-- exception created:>2012-12-31 -->
	Created since the beginning of 2013.
<!-- exception updated:<2013-01-01' -->
	Last updated before 2013.
```


>* [searching-users](https://help.github.com/articles/searching-users)

```
<!-- fullname:"Linus Torvalds"	 -->
Find users with the full name "Linus Torvalds".
<!-- tom location:"San Francisco, CA" -->
	Find all tom users in "San Francisco, CA".
<!-- chris followers:100..200	 -->
Find all chris users with followers between 100 and 200.
<!-- ryan repos:>10	 -->
Find all ryan users with more than 10 repositories.
```


#### 2.Trending

>* [trending](https://github.com/trending)


#### 3、快捷键

	
>* [github上按“shift + /”]

```
支持的快捷键：Site wide shortcuts、Repositories、Source code browsing

<!-- Site wide shortcuts -->

s or /	Focus search bar
g n	Go to Notifications
g d	Go to Dashboard
?	Bring up this help dialog
j	Move selection down
k	Move selection up
x	Toggle selection
o or enter	Open selection
h	Open hovercard
<!-- Repositories -->
g c	Go to Code
g i	Go to Issues
g p	Go to Pull Requests
g b	Go to Projects
g w	Go to Wiki
<!-- Source code browsing -->
t	Activates the file finder
l	Jump to line
w	Switch branch/tag
y	Expand URL to its canonical form
i	Show/hide all inline notes

```

>* [在特定的页面（Repositories），搜索文件按"t"，可以快速搜索](https://github.com/zhangkn/zhangkn.github.io/find/master)

```
You’ve activated the file finder. Start typing to filter the file list.
 1) Use ↑ and ↓ to navigate,
 2)enter to view files,
 3)esc to exit.

```

# see also 

>* [githubSearch](https://zhangkn.github.io/2018/04/githubSearch/)

>* [search](https://github.com/search)

>* [advanced search page.](https://github.com/search/advanced)

>* [searching-code](https://help.github.com/articles/searching-code)

>* [searching-in-forks](https://help.github.com/articles/searching-in-forks)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost githubSearch Advanced github search options -t Search
> #原来""的参数，需要自己加上""
```

