---
title: JavaScript对象的创建
categories:
  - frontend
tags:
  - JavaScript
  - Object-Oriented
date: 2017-03-01 22:01:21
---

在JavaScript中，最简单的创建对象的语法恐怕就是**new Object()**了，但是往往会遇到重复创建类似对象的情况，这种时候去手动一次次重复显然不合理，下面介绍几种常用的创建对象的模式。


## 工厂模式
工厂模式是一种广为人知的设计模式，这种模式抽象了创建具体对象的过程，而由于在JavaScript中没有类的概念，所以这里使用函数来代替，即使用函数来封装创建特定对象的细节。

```javascript
function createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayHi = function() {
    alert("Hello,I'm "+this.name+"!");
  };
  return o;
}
```

<!-- more -->

需要调用的时候就像这样


```javascript
var person1 = createPerson("Sean", 22, "Web Developer");
var person2 = createPerson("Wang", 23, "Engineer");
```


分析创建过程可以知道每次**createPerson()**函数按顺序接受指定的参数，同时创建一个新的对象作为初始对象，并将接受的参数作为这个对象的属性，最后返回这个对象。毫无疑问每次的创建都是一个新的对象，我们并不能知道这个对象属于什么类型。

## 构造函数模式
虽然JavaScript中没有类，但幸好有构造函数的存在，所以我们可以利用构造函数来进行对象的创建。

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayHi = function() {
    alert("Hello,I'm "+this.name+"!");
  }
}
```

而需要创建对象的时候则是想这样了

```javascript
var person1 = new Person("Sean", 22, "Web Developer");
var person2 = new Person("Wang", 23, "Engineer");
```

可以看到，构造函数模式和工厂模式有几个比较大的区别
- 没有显示地创建对象
- 直接将属性和方法赋给了this对象
- 没有return语句

值得一提的是一般构造函数始终都应该以一个**大写字母**开头，而非构造函数则是以一个小写字母开头，这种做法借鉴自其他的OO(Object-Oriented)语言，这样做主要为了区分其他的函数，因为本身构造函数也是函数，只不过可以用来创建对象而已。

这样得到的对象之间存在着联系，person1和person2都保存着Person的一个不同实例，它们都有一个constructor属性，该属性都指向Person。

```javascript
console.log(person1.constructor == Person); // true
console.log(person2.constructor == Person); // true
console.log(person1.constructor == person2.constructor); // true
```

尽管构造函数模式解决了我们确认一个实例类型的问题，但是仔细看一下**sayHi()**这个函数我们知道不同实例上的同名函数是不相等的，如果有兴趣可以通过以下代码验证以下。

```javascript
console.log(person1.sayHi === person2.sayHi); // false
```

而实际上为每个实例创建相同功能的函数实例完全没有必要，有人会想到把这个函数定义在构造函数外部来解决这个问题，但这样就破坏了封装性。

## 原型模式
针对上面提到的问题，原型模式能很好的解决。我们创建的每个函数都有一个**prototype**(原型)属性，这个属性是一个指向一个对象的指针，而这个对象的用途是包含由特定类型的所有实例共享的属性和方法，我们就称之为某个函数的原型。而prototype就是通过调用构造函数而创建的实例的原型对象。

```javascript
function Person() {
}

Person.prototype.name = "Sean";
Person.prototype.age = 22;
Person.prototype.job = "Web Developer";
Person.prototype.sayHi = function() {
  alert( "Hello,I'm " + this.name + "!" );
}
```

原型模式中，所有通过构造函数**Person()**创建的实例共享了name、age、job和sayHi属性。

## 构造和原型模式的结合
通过组合构造函数模式和原型模式我们能够讲实例属性和共享属性、共享方法的分离定义。

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = [ "Shelby", "Court" ];
}
Person.prototype.sayHi = function() {
  alert( "Hello,I'm " + this.name + "!");
}

var person1 = new Person("Sean", 22, "Web Developer");
var person2 = new Person("Wang", 23, "Engineer");
```

这样创建的实例有自己的实例属性name、age、job和friends，也存在有共享方法sayHi。同时，虽然friends是一个引用类型的属性，但由于是在构造函数中定义的，所以不同实例间的friends即便是引用类型也互不影响，单独存在。

## 动态原型模式

独立的构造函数和原型可能会让人困惑，所以有了动态原型模式的产生。

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Shelby","Court"];
  if(typeof sayHi != "function") {
    Person.prototype.sayHi = function() {
      alert("Hello,I'm "+this.name+"!");
    }
  }
}
```

这里将原型共享方法的定义逻辑放在了构造函数中，并且只有sayHi()方法不存在的情况下会被添加到原型中，同时所有实例立即共享该方法。

## 稳妥构造函数模式

道格拉斯.克罗克福德(Douglas Crockford)发明了JavaScript中的稳妥对象这个概念，该对象没有公共属性，方法不引用this的对象，适合禁止使用this和new的安全的环境，或者防止数据被改动的情况中使用。


```javascript
function Person(name, age, job) {
  var o = new Object();
  // 在这里可以定义私有变量
  o.sayHi = function(){
    console.log(name);
  };
  return o;
}

var sean = Person("Sean", 22, "Web Developer");
sean.sayHi(); // Hello I'm Sean
```

变量sean保存这一个稳妥对象，除了sayHi()方法没有别的方法可以访问其数据成员。

## 总结
能记多少记多少，理解万岁！
