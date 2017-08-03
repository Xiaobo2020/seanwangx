---
title: JavaScript之this
categories:
  - frontend
tags:
  - JavaScript
date: 2017-03-27 20:30:45
---


在JavaScript的使用过程中，this是被常常使用到的，但是对于JavaScript中的这个this之前都是学的一知半解，今天就在这里好好理一下this的相关概念，好好的了解一下这个时常让人头疼的this。

## 解释

这里先引用一下MDN中的解释

> In most cases, the value of this is determined by how a function is called. It can't be set by assignment during execution, and it may be different each time the function is called.

翻译过来就是在很多情况下，<b><i>this总是指向调用函数的那个对象</i></b>。所以一个函数被不同对象调用，那么this就很可能指向不同的对象。

<!-- more -->

## 不同场景

为了解释上文中提到的概念——<b><i>this总是指向调用函数的那个对象</i></b>，这里列举常用的场景，具体场景具体分析以下。

### 全局Global

在浏览器中，常规定义的变量其实都是可以理解为挂在window对象上的，所以这里的this即是window。

```javascript
var a = 1
function f () {
  console.log('Hello World!');
}
this.a // 1
this.f() // Hello World!

window.a // 1
window.f() // Hello World!

window === this // true
```

### 函数function

对于函数中的this就需要回顾刚才的概念——<b><i>this总是指向调用函数的那个对象</i></b>，所以需要具体问题具体分析。

```javascript
var x = 'show from window';
var obj = {
  x: 'show from obj'
};
function f () {
  console.log(this.x);
};

f(); // show from window
window.f(); // show from window
obj.f(); // show from obj
obj.f === window.f; // true
```

从上面的例子可以看出，函数是一个函数f，但是由于不同对象调用f，显示的信息不一样，从这里可以看出，this是跟着调用函数的对象走的。

### call和apply

之前提多的是大多数的情况，接下来介绍几种特殊的，最为常用的就是call和apply，两者的功能一样，都是更换调用函数所处的上下文，这里就可以理解为更换了this所指向的对象。附带提一下唯一不同的是，call接受的除第一个参数外其他为是列表参数，而apply接受的是数组参数，这里修改一下上个例子中的代码。

```javascript
var x = 'show from window';
var obj = {
  x: 'show from obj'
};
function f () {
  console.log(this.x);
};
obj.f.call(); // show from window
obj.f.call(this); // show from window
obj.f.call(window); // show from window
```

这里可以得出以下三个结论：

+ 上下文被改变，this指向全局window对象。
+ 缺省的参数默认为window/this。
+ 这里的window === this。


### bind

ECMAScript5中介绍了bind的用法，bind用于绑定函数上下文，且仅第一次bind有效。绑定之后无论谁调用了目标函数，上下文均不发生变化，总是指向bind的对象。

```javascript
function f () {
  return this.a;
}

var g = f.bind({a: 'azerty'});
console.log(g()); // azerty

var h = g.bind({a: 'yoo'}); // bind only works once!
console.log(h()); // azerty

var o = {a: 37, f: f, g: g, h: h};
console.log(o.f(), o.g(), o.h()); // 37, azerty, azerty
```

### 作为构造函数constructor

构造函数中的this用法可能都熟悉。

```javascript
function Person() {
  this.name = 'Sean';
  this.age = 21;
}

var person = new Person();
```

这里构造函数中的this指向的是实例化的那个对象——person。


### DOM节点中的this

this除了使用在function中，还会使用在DOM操作中，这个时候this就指向该节点本身，具体这里不做赘述。

## 总结

this的使用非常灵活，但是牢记其基本概念遇到问题去细细分析一般都能够解决，这里给两个阮一峰老师的博客中提到问题来练习一下this，看看对于this的概念是否理解了。


### 问题一

```javascript
var name = "The Window";
var object = {
  name : "My Object",
  getNameFunc : function(){
    return function(){
      return this.name;
    };
  }
};
console.log(object.getNameFunc()()); // The Window
```

### 问题二

```javascript
var name = "The Window";
var object = {
  name : "My Object",
  getNameFunc : function(){
    var that = this;
    return function(){
      return that.name;
    };
  }
};
console.log(object.getNameFunc()()); // My Object
```


## 参考

+ [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
+ [Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
+ [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
+ 《JavaScript高级程序设计（第3版）》
+ 《你不知道的JavaScript（上卷）》
