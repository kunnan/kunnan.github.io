---
layout: post
title: Sublime_plugin
date: 2018-05-22
tag: Sublime
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 小程序开发相关的插件下载
---



# [ESLint-Formatter ](https://github.com/TheSavior/ESLint-Formatter)

>* Sublime Text 3 Plugin to Autoformat with Eslint


```sh
/Users/devzkn/Library/Application Support/Sublime Text 3/Packages
devzkndeMacBook-Pro:Packages devzkn$ git clone git@github.com:TheSavior/ESLint-Formatter.git
```



>* 右键` ESLint Formatter > Format This File` 或者直接使用快捷键 `ctrl+shift+h`。如果快捷键冲突了，可以在菜单栏找到 `Preferences > Package Settings > ESLint Formatter > Key Binding - User`，打开 Key Binding - User 文件，新增快捷键绑定，代码如下：

```
{
    "keys": ["ctrl+alt+h"],
    "command": "format_eslint"
}
	 { "keys": ["ctrl+i"], "command": "reindent" },  //注意不要忘记加逗号,格式化

```


####  ESLint

`ESLint `用于静态检查代码的语法和风格，安装命令如下。
```
$ npm install --save-dev eslint babel-eslint
```

```
 "devDependencies": {
    "babel-eslint": "^7.2.1",
```






# Sublime 支持`wpy` 语法高亮的配置步骤

>*   1. 打开Sublime->Preferences->Browse Packages..进入用户包文件夹。

>*   2. 在此文件夹下打开cmd，运行`git clone git@github.com:vuejs/vue-syntax-highlight.git`，进行下载[`vue-syntax-highlight`](https://github.com/vuejs/vue-syntax-highlight),或者直接下载[`zip`](https://github.com/vuejs/vue-syntax-highlight/archive/master.zip)包解压至当前文件夹。
>```
>devzkndeMacBook-Pro:szjf_html devzkn$ cd /Users/devzkn/Library/Application\ Support/Sublime\ Text\ 3/Packages 
>```
>



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Sublime_plugin 小程序开发相关的插件下载 -t Sublime
> #原来""的参数，需要自己加上""
```

