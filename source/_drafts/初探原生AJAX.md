---
title: 初探原生AJAX
categories:
  - frontend
tags:
  - JavaScript
date: 2017-03-26 22:29:06
---

目前对于AJAX的使用我们可以借助于封装好的工具，比如jQeruy的$.ajax()，又或者vue中的vue-resource、axios，工具很多，但是对于基本的实现之前却是一知半解，今天在这里来利用原生javascript来实现一下这个在现代web开发中不可缺少的通信方法。

## XMLHttpRequest对象

由于浏览器兼容性（主要是IE），所以创建XMLHttpRequest对象的方法也有好几种，这里可以创建一个函数，专门用来针对不同浏览器兼容性问题创建XHR对象。

```javascript
function getHTTPObject () {
  if (typeof XMLHttpRequest != 'undefined') {
    return new XMLHttpRequest(); // 存在现有XHR对象，直接返回
  } else if (typeof ActiveXObject != 'undefined') {
    try { return new ActiveXObject('Msxml2.XMLHTTP.6.0'); } catch (e) {}
    try { return new ActiveXObject('Msxml2.XMLHTTP.3.0'); } catch (e) {}
    try { return new ActiveXObject('Msxml2.XMLHTTP'); } catch (e) {}
    return null
  } else {
    return null;
  }
}
```

现在通过函数getHTTPObject我们可以获得一个XHR对象或者为null。

## XHR用法

获得了对象之后就是如何使用的问题了，XMLHttpRequest对象有许多的方法，最有用的事open方法，它可以用来指定服务器上将要访问的文件，指定请求类型：GET、POST和SEND。除了请求类型和请求对象，open方法还接受第三个参数——用于指定请求是否以异步方式发送和处理。

```javascript
function getNewContent () {
  let request = getHTTPObject();
  if (request != null) {
    request.open('GET', 'example.txt', true);
    request.onreadystatechange = function () {
      if (request.readyState === 4) {
        // code here 处理响应
        let para = document.createElement('p');
        let txt = document.createTextNode(request.responseText);
        para.appendChild(txt);
        document.getElementsByTagName('body')[0].appendChild(para);
      }
    };
    request.send(null);
  } else {
    console.log('Sorry, your browser doesn\'t support XMLHttpRequest');
  }
}
```

上述方法执行后会请求同目录下的example.txt文件（可能会有浏览器访问本地文件限制，此处不做赘述）。代码中的onreadystatechange是一个事件处理函数，他会在服务器给XHR对象送回响应的时候被触发执行。