---
layout: post
title: flex_layout
date: 2018-05-21
tag: flex
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Flex 布局教程：语法篇
---


# 前言


# demo

>* [demo：动态显示`items、container `的各个属性对应的效果](https://codepen.io/justd/full/yydezN/)
>

####  demo code
>* [demo code](https://github.com/wepyminiprogram/flexbox-playground)
>*  npm install --global gulp
>```
>WARN notice [SECURITY] minimatch has 1 high vulnerability. Go here for more details: https://nodesecurity.io/advisories?search=minimatch&version=2.0.10 - Run `npm i npm@latest -g` to upgrade your npm version, and then `npm audit` to get more info.
>```
>*  npm install
>```
>Compass is charityware. If you love it, please donate on our behalf at http://umdf.org/compass Thanks!
npm notice created a lockfile as package-lock.json. You should commit this file.
>```
>* gulp
>```
>[17:08:22] Ignoring ffi-1.9.8 because its extensions are not built. Try: gem pristine ffi --version 1.9.8
>directory dist/css
    write dist/css/style.css
    [17:08:24] Finished 'build-css' after 2.36 s
[17:08:24] Starting 'default'...
[17:08:24] Finished 'default' after 31 μs
>```
>
>* [`open file:///Users/devzkn/code/miniprogramDemo/flexbox-playground/dist/index.html` 即可查看效果](https://cssflex.github.io/)
>
>
>*  [https://cssflex.github.io/](https://cssflex.github.io/)
>


#### Mini project demonstrating how to make Chrome-like tabs


>* [knchrome-tabs](https://cssflex.github.io/knchrome-tabs/)
>


# flexbox-basics


![image](https://wx4.sinaimg.cn/large/006tBeITgy1frj5fb7vdqj30yv0ciad5.jpg)


>* `the flexbox model`: The flex layout is constituted of parent container referred as` flex container` and its immediate children which are called` flex items`.


![image](http://wx4.sinaimg.cn/large/006tBeITgy1friys2d6f5j30dw05kjra.jpg)


![image](http://wx4.sinaimg.cn/large/006tBeITgy1friyscvpokj30dw05kjrb.jpg)






# Properties for the Parent（flex container)




#### display


>* This defines a flex container,It enables a flex context for all its direct children.
>```css
>.container {
  display: flex; /* or inline-flex */
}
>```
>


#### flex-direction


![image](http://wx4.sinaimg.cn/large/006tBeITgy1friz6ua07xj308c01y3ya.jpg)


```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

>* row (default): left to right in ltr; right to left in rtl
>* row-reverse: right to left in ltr; left to right in rtl
>* column: same as row but top to bottom
>* column-reverse: same as row-reverse but bottom to top


#### flex-wrap

![image](http://wx4.sinaimg.cn/large/006tBeITgy1frizi1521fj308v034743.jpg)

By default, flex items will all try to fit onto one line. You can change that and allow the items to wrap as needed with this property.

>```css
>.container{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
>```
>
>

>* nowrap (default): all flex items will be on one line
>* wrap: flex items will wrap onto multiple lines, from top to bottom.
>* wrap-reverse: flex items will wrap onto multiple lines from bottom to top.
>
>* [There are some visual demos of flex-wrap here](https://css-tricks.com/almanac/properties/f/flex-wrap/)
>
>
>

#### flex-flow (Applies to: parent flex container element)
This is a shorthand` flex-direction` and` flex-wrap `properties, which together define the flex container's main and cross axes. Default is` row nowrap`.

```css
flex-flow: <‘flex-direction’> || <‘flex-wrap’>
    flex-flow:row wrap;
```


#### justify-content


![image](http://wx4.sinaimg.cn/large/006tBeITgy1frizxipvtij302x046jr7.jpg)

```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
}
```


>* flex-start (default): items are packed toward the start line
>* flex-end: items are packed toward to end line
>* center: items are centered along the line
>* space-between: items are evenly distributed in the line; first item is on the start line, last item on the end line
>* space-around: items are evenly distributed in the line with equal space around them. Note that visually the spaces aren't equal, since all the items have equal space on both sides. The first item will have one unit of space against the container edge, but two units of space between the next item because that next item has its own spacing that applies.
>* space-evenly: items are distributed so that the spacing between any two items (and the space to the edges) is equal.


#### align-items


![image](http://wx4.sinaimg.cn/large/006tBeITgy1frj02zdrcfj308v0aht8s.jpg)


```css
.container {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

>* flex-start: cross-start margin edge of the items is placed on the cross-start line
>* flex-end: cross-end margin edge of the items is placed on the cross-end line
>* center: items are centered in the cross-axis
>* baseline: items are aligned such as their baselines align
>* stretch (default): stretch to fill the container (still respect min-width/max-width)
>


#### align-content

![image](http://wx4.sinaimg.cn/large/006tBeITly1frj0i6uga7j308v0aht8u.jpg)

>* Note: this property has no effect when there is only one line of flex items.

```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```



>* flex-start: lines packed to the start of the container
>* flex-end: lines packed to the end of the container
>* center: lines packed to the center of the container
>* space-between: lines evenly distributed; the first line is at the start of the container while the last one is at the end
>* space-around: lines evenly distributed with equal space around each line
>* stretch (default): lines stretch to take up the remaining space


# Properties for the Children(flex items)


#### order

![image](http://wx4.sinaimg.cn/large/006tBeITgy1frj0kbeab1j30680460sj.jpg)

```css
.item {
  order: <integer>; /* default is 0 */
}
```


#### flex-grow

```css
.item {
  flex-grow: <number>; /* default 0 */
}
```

#### flex-shrink



```css
.item {
  flex-shrink: <number>; /* default 1 */
}
```

#### flex-basis


```css
.item {
  flex-basis: <length> | auto; /* default auto */
}
```

#### flex

```css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

#### align-self

![image](https://wx4.sinaimg.cn/large/006tBeITgy1frj1lba6rsj308v03qjr8.jpg)

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```






# See Also 

>* [a-visual-guide-to-css3-flexbox-properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)
>

>* [A Complete Guide to Flexbox
](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)


>* [flex-box-demo](https://github.com/JailBreakC/flex-box-demo)
>
>

>* [Introduction_to_the_CSS_box_model](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)
>
>

>* [flex](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost flex_layout Flex 布局教程：语法篇 -t flex
> #原来""的参数，需要自己加上""
```

