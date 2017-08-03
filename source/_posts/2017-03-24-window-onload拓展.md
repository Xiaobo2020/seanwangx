---
title: window.onload拓展
categories:
  - frontend
tags:
  - JavaScript
date: 2017-03-24 23:09:06
---


学习使用JavaScript的过程中，不可避免会遇到一个有趣的属性——window.onload，本文主要介绍一下window.onload的用法、与JQuery中类似方法的区别以及一点其他有趣的拓展。

## 基础介绍

我们知道在html文档中使用JavaScript主要有两种方式，一种是直接在文档中定义并使用JS代码，另外一种则是通过&lt;script&gt;的src属性外联一个js文件，在外联的js文件中去定义相关的代码。


但是，不论是内嵌还是外联，都会遇到一个何时加载的问题，一般来说，浏览器解析文档结构的时候，解构到哪里就会在何时去执行。同时在执行相关操作的时候会影响整体页面的加载，所以一般来说不管是内嵌的JS代码还是标签外联JS文件，其存放的位置都会在文档body标签的最后，这样可以最大化的提高网页加载速度。

值得一提的是，当网页所有内容加载完成的时候，浏览器会触发一个属性，那就是window.onload.

<!-- more -->

## window.onload与$(document).ready()

常规JS的load属性语法如下

```javascript
window.onload = funcRef;
```

作用是在文档内容全部加载结束后，window对象的load属性呗调用，执行相关函数。使用过JQuery的可能马上会想到那个功能类似的函数。

```javascript
$(document).ready(function(){
  // code here ..
});

// 或者
$(function(){
  // code here ...
});
```

两者功能类似，但是window.onload需要文档内容包括图片之类的全部加载完成后触发执行，而$(document).ready()则是等待DOM结构加载完成立刻调用执行，所以细细分析还是能发现一些区别。

| 特性 | window.onload | $(document).ready() |
| ------| ------ | ------ |
| 定义次数 | 唯一 | 可多次 |
| 触发时间 | 全部内容加载结束 | DOM结构加载技术 |
| 简化 | 无 | $(function(){}) |

## 小技巧

在阅读《JavaScript DOM编程艺术》（第2版）艺术的时候偶然发现了一种针对window.onload的使用小技巧，这里分享以下。起因是在常规的写法中，如果我需要触发多个function，那么写法一般都是这个样子

```javascript
window.onload = function() {
    funA();
    funB();
    // ...
    funN();
};
```

在处理需要绑定的函数不是很多的场合，这应该是最简单的解决方案，但是在书中提到了一个弹性最佳的方案——由Simon Willison （详见 [http://simon.incutio.com](http://simon.incutio.com)）编写的addLoadEvent函数。addLoadEvent这个函数只有一个参数：打算在页面加载完毕时执行的函数的名字，而addLoadEvent函数将要完成的操作如下

+ 把现有的window.onload事件处理函数的值存入变量oldonload
+ 如果在这个处理函数上还没有绑定任何函数，就像平时那样把新函数添加给window.onload
+ 如果在这个处理函数上已经绑定了一些函数，就把新函数追加到现有指令的末尾。

```javascript
function addLoadEvent(func) {
    var oldonload = window.onload;
    if (typeof window.onload != 'function') {
      window.onload = func;
    } else {
      window.onload = function() {
        oldonload();
        func();
      }
    }
}

addLoadEvent(funA);
addLoadEvent(funB);
```

虽然需要额外编写的一些代码，但是对于后期代码的维护非常有帮助，无论页面加载完毕后需要执行多少函数，只需要添加一行语句即可。