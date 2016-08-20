---
layout: 	post
title: 		非IE浏览器里对style.width赋值无效的问题
catalog: 	true
tags: 		[Window, 前端, JS, ]
description: 关于iframe高度的设置是由于设置页面使用XHTML DTD校验导致设置不带单位的width无效
---

## 非IE浏览器里对style.width赋值无效的问题
> 在做JSP页面调试div的高度的时候，在IE或IE内核浏览器里都已经测试通过没有任何问题，但是在**Chrome、Mozilla、Firefox、Netscape**里测试时问题就来了

我的页面里用到了 **iframe**，这些 **iframe** 初始的 **style.height** 都是0，而在加载页面之后，iframe 就会自适应被加载的页面高度，我是通过 

```
iframe.style.height=300
```

这样撑起 iframe 的高度，这样处理在IE系列浏览器里没有任何问题，但在非IE浏览器里死活行不通。

通过跟踪，发现根本没有把这个 300 赋给 **style.height**，最后测试出来竟然必须给定赋值的单位，即 

```
iframe.style.height="300px"
```

这样赋值才有效，没有单位的赋值无效，怎么会如此厚颜无耻！

说明一下环境：我用的是**XHTML**的**DTD**，即在页面头上是：

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

也正是因为这个 **XHTML**的**dtd**文件 才导致非IE浏览器里的 **style.height** 赋值有问题。
##### 另外透露一点知识
在**Netsacpe**里的iframe，若通过设置 `style.display="none"` 隐藏这个 iframe 的话，会把通过脚本动态写入到 iframe 的HTML“冲掉”。所以在 Netscape 里的 iframe 若有脚本写入HTML又需要动态隐藏/显示这个 iframe 的操作时建议你使用

```
 style.width="0px"  style.height="0px"; 
```

 这样的方式隐藏！