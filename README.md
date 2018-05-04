# Blog

> 
> 
>* 我看中这个版本的catalog，因此放弃了[旧的博客](https://github.com/zhangkn/zhangkn.github.io)
>
### [新的博客在这里 &rarr;](https://kunnan.github.io/)


>* [feed](https://kunnan.github.io/feed.xml)
>

## 使用

* 开始
	* [环境](#环境)
	* [开始](#开始)
	* [撰写博文](#撰写博文)
* 组件
	* [侧边栏](#侧边栏)
	* [迷你关于我](#mini-about-me)
	* [推荐标签](#featured-tags)
	* [好友链接](#friends)
	* [HTML5 演示文档布局](#keynote-layout)
* 评论与 Google/Baidu Analytics
	* [评论](#comment)
	* [网站分析](#analytics) 
* 高级部分
	* [自定义](#customization)
	* [标题底图](#header-image)
	* [搜索展示标题-头文件](#seo-title)



### 环境

安装了 [jekyll](http://jekyllcn.com/)之后，`http://127.0.0.1:4000/`预览效果，对文章的修改也能实时展示效果（需要强刷浏览器）。

>* 本地调试博客的官方例子
>
>```
>~ $ gem install jekyll bundler
~ $ jekyll new my-awesome-site
~ $ cd my-awesome-site
~/my-awesome-site $ bundle install
~/my-awesome-site $ bundle exec jekyll serve
=> 打开浏览器 http://localhost:4000
>```

>* plugins: [jekyll-paginate]
>
```
 1)使用了jekyll plugins之后，除了在_config.yml声明：
  plugins: [jekyll-paginate]；
 2)还需要创建对应的Gemfile
 source 'https://rubygems.org'
gem 'jekyll'
gem 'redcarpet'
gem 'jekyll-paginate'
```

>* alias

```
alias jekyll='bundle exec jekyll'

alias cdio='cd ~/githubPages/zhangkn.github.io'

alias cdpost='cd ~/githubPages/kunnan.github.io.git'


alias sio='bundle exec jekyll server -s ~/githubPages/zhangkn.github.io'

alias spost='bundle exec jekyll server -s ~/githubPages/kunnan.github.io.git'

<!--是配置生效运行spro-->
alias spro='source ~/.bash_profile'
```

### Q&A

>* Q：Could not locate Gemfile or .bundle/ directory

```
A：新增Gemfile
source 'https://rubygems.org'
gem 'jekyll'
gem 'redcarpet'
gem 'jekyll-paginate'
gem 'jekyll-sitemap'
gem 'jekyll-feed'
# gem 'github-pages', group: :jekyll_plugins
```
>* Q： The 'gems' configuration option has been renamed to 'plugins'. Please update your config file accordingly.
```
gems: [jekyll-paginate]--- 错误
plugins: [jekyll-paginate]----正确
```

>* Q：Could not find public_suffix-3.0.0 in any of the sources 
```
devzkndeMacBook-Pro:zhangkn.github.io devzkn$ Bundler update
```

>* Q:    Liquid Warning: Liquid syntax error (line 142): Unexpected character { in "tag[1].size > {{site.featured-condition-size}}" in /_layouts/post.html
```
A：根据 https://github.com/github/pages-gem, Github Pages 目前使用的是 Jekyll 3.0.2，so...let's the only version I targeted at.
Using liquid 4.0.0
https://github.com/Huxpro/huxpro.github.io/issues/105
-                            {% if tag[1].size > {{site.featured-condition-size}} %}
+                            {% if tag[1].size > site.featured-condition-size %}
```
>* Q:Incremental build: disabled. Enable with --incremental
>```
>alias spost='bundle exec jekyll server -s  ~/githubPages/kunnan.github.io.git --incremental'
> bundle exec jekyll serve --drafts --incremental
>  Incremental build: enabled
>```

### 开始

你可以通用修改 `_config.yml`文件来轻松的开始搭建自己的博客:

```
# Site settings
title:  Blog                    # 你的博客网站标题
SEOTitle: 的博客 |  Blog		# SEO 标题
description: ""	   	   # 随便说点，描述一下

# SNS settings      
github_username:     # 你的github账号
jianshu_username:  # 你的简书ID。

# Build settings
# paginate: 10              # 一页你准备放几篇文章
```

Jekyll官方网站还有很多的参数可以调，比如设置文章的链接形式...网址在这里：[Jekyll - Official Site](http://jekyllrb.com/) 中文版的在这里：[Jekyll中文](http://jekyllcn.com/).

### 撰写博文

要发表的文章一般以 **Markdown** 的格式放在这里`_posts/`，支持子目录。
yaml 头文件长这样:

```
---
layout:     post
title: CycriptTricks
date: 2018-04-20
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Powerful_private_methods
header-img: img/post-bg-ios9-web.jpg
tags:
    - iOS
    - 定时器
---

```

>* [直接使用 `tree` 命令，会在当前文件目录下，递归输出所有文件层级,从中可以看出_posts 支持子目录](https://kunnan.github.io/2017/03/07/tree/)

```
├── _posts
│   ├── 2016-12-07-about_xcode.md
│   ├── 2017-2-9-Mac_Automator.md
│   ├── GoogleMethodology
│   │   ├── 2018-04-22-Beyond_Free.md
│   │   ├── 2018-04-22-TheWayOfThinking.md
│   │   ├── 2018-04-22-Venture_Capital_Investment.md
│   │   └── 2018-04-22-currency.md
│   ├── OutlineOfChineseHistory
│   │   ├── 2018-04-21-Feudal_society.md
│   │   └── 2018-04-21-Powerful_Clan_Society.md
│   ├── Search
│   │   ├── 2018-04-23-GoogleHacking.md
│   │   └── 2018-04-24-githubSearch.md
│   ├── iget
│   │   └── 2018-04-21-Effectively_Enhance_The_Value_Of_Workplace.md
│   └── iosre
│       ├── 2018-04-20-CycriptTricks.md
│       └── 2018-04-20-how_to_host_cydia_repo.md
```

>* [辅助脚本knpost](https://github.com/zhangkn/KNBin/blob/a68c51359c2f4f315982c94487e04080abd60875/knpost)

```
<!--标题、子标题、标签-->
devzkndeMacBook-Pro:kunnan.github.io.git devzkn$ knpost CycriptTricks Powerful_private_methods -t iosre
devzkndeMBP:kunnan.github.io.git devzkn$ knpost Feudal_society 中国史纲之封建社会 -t OutlineOfChineseHistory
```

### 侧边栏


设置是在 `_config.yml`文件里面的`Sidebar settings`那块。

```
# Sidebar settings
sidebar: true  #添加侧边栏
sidebar-about-description: "简单的描述一下你自己"
sidebar-avatar: /img/.jpg     #你的大头贴，请使用绝对地址.注意：名字区分大小写！后缀名也是
```

侧边栏是响应式布局的，当屏幕尺寸小于992px的时候，侧边栏就会移动到底部。具体请见bootstrap栅格系统 <http://v3.bootcss.com/css/>


### Mini About Me

Mini-About-Me 这个模块将在你的头像下面，展示你所有的社交账号。这个也是响应式布局，当屏幕变小时候，会将其移动到页面底部，只不过会稍微有点小变化，具体请看代码。

### Featured Tags

看到这个网站 [Medium](http://medium.com) 的推荐标签非常的炫酷，所以我将他加了进来。
这个模块现在是独立的，可以呈现在所有页面，包括主页和发表的每一篇文章标题的头上。

```
# Featured Tags
featured-tags: true  
featured-condition-size: 1     # A tag will be featured if the size of it is more than this condition value
```

唯一需要注意的是`featured-condition-size`: 如果一个标签的 SIZE，也就是使用该标签的文章数大于上面设定的条件值，这个标签就会在首页上被推荐。
 
内部有一个条件模板 `{% if tag[1].size > {{site.featured-condition-size}} %}` 是用来做筛选过滤的.

### Social-media Account

在下面输入的社交账号，没有的添加的不会显示在侧边框中。新加入了[简书](https:/www.jianshu.com)链接, <http://www.jianshu.com/u/e71990ada2fd>

	# SNS settings
	RSS: false
	jianshu_username: 	jianshu_id 
	zhihu_username:     username
	facebook_username:  username
	github_username:    username
	# weibo_username:   username
	
	

![](http://ww4.sinaimg.cn/large/006tKfTcgy1fgrgbgf77aj308i02v748.jpg)

### Friends

好友链接部分。这会在全部页面显示。

设置是在 `_config.yml`文件里面的`Friends`那块，自己加吧。

```
# Friends
friends: [
   
]
```


### Keynote Layout

HTML5幻灯片的排版：

![](https://camo.githubusercontent.com/f30347a118171820b46befdf77e7b7c53a5641a9/687474703a2f2f6875616e677875616e2e6d652f696d672f626c6f672d6b65796e6f74652e6a7067)

这部分是用于占用html格式的幻灯片的，一般用到的是 Reveal.js, Impress.js, Slides, Prezi 等等.我认为一个现代化的博客怎么能少了放html幻灯的功能呢~

其主要原理是添加一个 `iframe`，在里面加入外部链接。你可以直接写到头文件里面去，详情请见下面的yaml头文件的写法。

```
---
layout:     keynote
iframe:     "http://huangxuan.me/js-module-7day/"
---
```

iframe在不同的设备中，将会自动的调整大小。保留内边距是为了让手机用户可以向下滑动，以及添加更多的内容。[例子：](https://github.com/Huxpro/huxpro.github.io/blob/4bcfb7543bce3c2a771368ba40a2c838668ad994/_posts/2016-11-20-sw-101-gdgdf.markdown)


### Comment

博客不仅支持 [Disqus](http://disqus.com) 评论系统,还加入了 [Gitalk](https://gitalk.github.io/) 评论系统，[支持 Markdwon 语法](https://guides.github.com/features/mastering-markdown/)，cool~

#### Disqus

优点：国际比较流行，界面也很大气、简洁，如果有人评论，还能实时通知，直接回复通知的邮件就行了；

缺点：评论必须要去注册一个disqus账号，分享一般只有Facebook和Twitter，另外在墙内加载速度略慢了一点。

**使用：**
https://disqus.com/admin/create/ 

**其次**，你只需要在下面的 yaml 头文件中设置一下就可以了。

```
# 评论系统
# Disqus（https://disqus.com/）
disqus_username: qiubaiying
```

#### Gitalk

优点：界面干净简洁，利用 Github issue API 做的评论插件，使用 Github 帐号进行登录和评论，最喜欢的支持 Markdown 语法，对于程序员来说真是太 cool 了。

缺点：配置比较繁琐，每篇文章的评论都需要初始化。

**使用：**

获取clientID、clientSecret：[new github.com applications](https://github.com/settings/applications/new)


### Analytics

网站分析，现在支持百度统计和Google Analytics。需要去官方网站注册一下，然后将返回的code贴在下面：

```
# Baidu Analytics
ba_track_id:

# Google Analytics
ga_track_id: 'UA--1'            # 你用Google账号去注册一个就会给你一个这样的id
ga_domain:			# 默认的是 auto, 这里自定义的域名，如果没有自己的域名，需要改成auto。
```

### Customization

如果你喜欢折腾，你可以去自定义这个模板的 Code。

**如果你可以理解 `_include/` 和 `_layouts/`文件夹下的代码（这里是整个界面布局的地方），你就可以使用 Jekyll 使用的模版引擎 [Liquid](https://github.com/Shopify/liquid/wiki)的语法直接修改/添加代码，来进行更有创意的自定义界面啦！**

### Header Image

每一篇文章可以有不同的底图，宽度要够，大小不要太大，否则加载慢。

> 上传的图片最好先压缩，这里推荐 imageOptim 图片压缩软件。

但是需要注意的是本模板的标题是**白色**的，所以背景色要设置为**灰色**或者**黑色**，总之深色系就对了。

### SEO Title

### favicon.ico

>* [How To Use Favicon](http://www.faviconico.org/favicon)
>
>```
>多种浏览器中网页title的左边，收藏夹栏和书签栏中
> <!-- Favicon -->
    <link rel="shortcut icon" href="{{ site.baseurl }}/img/favicon.ico">
    <link rel="bookmark" href="{{ site.baseurl }}/img/favicon.ico">
        <link rel="icon"  href="{{ site.baseurl }}/img/favicon.ico" type="image/x-icon" />
>```

### Apple touch icon

>* [Apple touch icon(Safari Webpage Icon)](https://www.favicon-generator.org/)
>```
><script src="https://gist.github.com/zhangkn/e10655836d6cd025847ec46e0fa88cc0.js"></script>
```
>* [code](https://gist.github.com/zhangkn/e10655836d6cd025847ec46e0fa88cc0)


>* [Then include the following code in the head of your HTML document.](https://www.favicon-generator.org/)
>```
><link rel="apple-touch-icon" sizes="57x57" href="/apple-icon-57x57.png">
<link rel="apple-touch-icon" sizes="60x60" href="/apple-icon-60x60.png">
<link rel="apple-touch-icon" sizes="72x72" href="/apple-icon-72x72.png">
<link rel="apple-touch-icon" sizes="76x76" href="/apple-icon-76x76.png">
<link rel="apple-touch-icon" sizes="114x114" href="/apple-icon-114x114.png">
<link rel="apple-touch-icon" sizes="120x120" href="/apple-icon-120x120.png">
<link rel="apple-touch-icon" sizes="144x144" href="/apple-icon-144x144.png">
<link rel="apple-touch-icon" sizes="152x152" href="/apple-icon-152x152.png">
<link rel="apple-touch-icon" sizes="180x180" href="/apple-icon-180x180.png">
<link rel="icon" type="image/png" sizes="192x192"  href="/android-icon-192x192.png">
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="96x96" href="/favicon-96x96.png">
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
<link rel="manifest" href="/manifest.json">
<meta name="msapplication-TileColor" content="#ffffff">
<meta name="msapplication-TileImage" content="/ms-icon-144x144.png">
<meta name="theme-color" content="#ffffff">
>```


### 关于收到"Page Build Warning"的 Email

由于jekyll升级到3.0.x,对原来的 pygments 代码高亮不再支持，现只支持一种-rouge，所以你需要在 `_config.yml`文件中修改`highlighter: rouge`.另外还需要在`_config.yml`文件中加上`gems: [jekyll-paginate]`.

同时,你需要更新你的本地 jekyll 环境.

使用`jekyll server`的同学需要这样：

1. `gem update jekyll` # 更新jekyll
2. `gem update github-pages` #更新依赖的包

使用`bundle exec jekyll server`的同学在更新 jekyll 后，需要输入`bundle update`来更新依赖的包.

> Note：
> 可以使用 `jekyll -s` 命令在本地实时配置博客，提高效率。详见 [Jekyll.com](http://jekyllcn.com/)

参考文档：[using jekyll with pages](https://help.github.com/articles/using-jekyll-with-pages/) & [Upgrading from 2.x to 3.x](http://jekyllrb.com/docs/upgrading/2-to-3/)


## 致谢

 感谢 Jekyll、Github Pages 和 Bootstrap!

## License

遵循 MIT 许可证。有关详细,请参阅 [LICENSE](https://github.com/qiubaiying/qiubaiying.github.io/blob/master/LICENSE)。

# [kunnan.github.io](https://kunnan.github.io)
