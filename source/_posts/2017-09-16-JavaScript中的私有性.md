---
title: JavaScript中的私有性
categories:
  - frontend
tags:
  - Private
date: 2017-09-16 22:32:01
---

# 前言
严格来说，JavaScript中是没有`类`的，但是我们可以通过对象原型实现`类`，到了es6中，`class`被引入，但它也主要是JavaScript现有的基于原型的继承的语法糖，这里暂且成为JavaScript中的类。在这个理解的基础上将JavaScript同其他OO语言如C++、Java等比较，我对于JavaScript中的私有性产生了些好奇，也在这里记录一些自己的思考。

<!-- more -->

# 私有属性
所谓私有性无非就是作用域范围被加了一定的限制，在常规对象和es6中，私有属性有着不同的实现。

## function中的私有属性
常规创建对象的方法用的是`function`，同时配合`this`来进行公共属性的定义，而要添加私有属性在function中非常方便，可以理解为定义在function内部的变量就是私有属性。

```javascript
function SuperType () {
  this.props = 'public variable'
  selfProps = 'private variable'
}

let instance = new SuperType()

console.log(instance.props) // ‘public variable'
console.log(instance.selfProps) // undefined
```

可以看到新创建的实例能访问到`props`，而不能访问`selfProps`，这就是私有属性。比较有意思的是有的时候会需要对象原型上的函数访问私有属性，目前找到的一种解决方式是定义语句在对象内部实现，比如这样。

```javascript
function SuperType () {
  this.props = 'public variable'
  selfProps = 'private variable'
  SuperType.prototype.visit = function () {
    console.log(selfProps)
  }
}

let instance1 = new SuperType()
let instance2 = new SuperType()
console.log(instance1.visit === instance2.visit) // true
instance1.visit() // 'private variable'
```

通过`===`可以判定visit是定义在原型上的公共函数，但是这里能访问到实例中的私有属性。

## class中的私有属性
需要明确一点，es6不支持私有属性，但是目前有一个提案，为`class`加了私有属性，方法是在私有属性前加`#`：

```javascript
class Point () {
  #x = 0
  printX () {
    console.log(#x)
  }
}
```

如果想要稳妥地在class中实现私有属性的定义，还有一种比较取巧的方法就是利用模块。也就是说将模块作为一个作用域，对外的借口是class，但是在模块中、class外定义变量，因为模块的访问限制外部只能访问到class，这样定义在模块中的变量自然就专为class所有，这是私有属性的另一种实现。

# 私有方法
和私有属性相似，私有方法在es5和es6中实现也是不同的。

## function中的私有方法
es5中实现私有方法就像实现私有属性一样简单，就是定义在函数内部。

```javascript
function SuperType () {
  // 公共方法
  this.sayHi = function () {
    console.log('Hi')
    sayHello()
  }

  // 私有方法
  function sayHello () {
    console.log('Hello')
  }
}

let instance = new SuperType()
instance.sayHi()
// 'Hi'
// 'Hello'

instance.sayHello()
// Uncaught TypeError: instance.sayHello is not a function
```

## class中的私有方法
和私有属性一样，es6中没有私有方法。但是就像私有属性一样，我们可以利用模块的特性，曲线的实现私有方法。另外有一种就是利用Symbol值的唯一性，这里借用一下阮一峰老师书中的例子。

```javascript
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
```

# 结束
es6加入了一大波新的内容，可以说是比较重要的更新，当然我想说，class都有了，private还会远吗？
