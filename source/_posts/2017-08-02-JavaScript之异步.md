---
title: JavaScript之异步
categories:
  - frontend
tags:
  - Callback
  - Promise
date: 2017-08-02 23:07:06
---

# 前言
套用《你不知道的JavaScript(中卷)》中的说法，这是一篇讨论现在与未来的文章，因为异步要解决的本质问题我觉得就是把握现在与未来不同阶段中程序运行的控制权。我曾经问过我的一个朋友——“什么是异步”，他也做了一段简短的回答，但是我听下来之后就发现了一个问题，在他的理解中，错误的把“异步”这个名词冠名在了“并行”的概念上。而事实上，我相信很多人都和他一样将术语“异步”和“并行”混为一谈了。

简短来说，***异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情***。

<!-- more -->

# 最基础的异步模式
说到异步不能不说一下回调(callback)，没错，回调就是JavaScript这门语言中最基础的异步模式。现在来一段代码理解一下异步和回调：

```javascript
console.log('now')
function later () {
  console.log('later')
}
setTimeout(() => { later() }, 2000)
```

上面五行代码中，部分是现在运行，部分是将来运行。
现在：
```javascript
console.log('now')
function later () { ... }
setTimeout(() => { later() }, 2000)
```

将来：
```javascript
console.log('later')
```

可以很清楚的看到这里使用<code>setTimeout</code>来进行了一次异步操作，其中“将来”运行的部分其实是定义在了<code>later</code>这个函数中的，这个例子中的回调就是<code>setTimeout</code>的第一个参数——一个箭头函数，在回调中又运行了一下<code>later</code>函数。

现在web开发中使用ajax来进行与后台的数据交互，而得到数据后常用的就是通过callback回调来处理接下来的操作。通过回调，我们可以按照自己的设想来保证程序运行的顺序。但是回调也存在一个问题，一旦存在多个回调嵌套（常被成为回调地狱[callback hell](http://callbackhell.com/)），那么代码的可读性就会变得非常的差，同时代码的追踪、调试以及后期维护都会变得非常困难。

# Promise
ES6原生提供了[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)，主要用于处理异步请求，从字面理解就是一个承诺，假如成功就XXX，如果失败就XXX。

## 语法
Promise的语法很简单：
```javascript
new Promise(
  /* executor */
  function (resolve, reject) {
    // TODO
  }
)
```
TODO部分就是我们的异步操作，成功调用resolve，失败则调用reject。就像executor中存在resolve和reject两个出口，Promise对象对于成功和失败的结果也有不同的操作方式，即then和catch。看个简单的例子：
```javascript
function foo () {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      let num = Math.floor(Math.random() * 10)
      if (num % 2 === 0) {
        resolve(num)
      } else {
        reject(num)
      }
    }, 2000)
  })
}
foo().then(function (even) {
  console.log('get even number', even)
}).catch(function (odd) {
  console.log('get odd number', odd)
})
```
延时2000毫秒，获取一个随机数，偶数用resolve返回，在then中处理；奇数用reject返回，在catch中处理。

## 优点
从上一个例子的语法上就知道，Promise使用链式的结果处理，和普通的嵌套回调相比，可读性大大提高了。值得一提的是Promise的then和catch返回的也是一个Promise对象，这也是可以链式调用的原因。

曾经我和一个朋友讨论是该使用Callback还是Promise，他给出的理由很有意思：Callback是将函数的执行权给了异步操作，而Promise则保证了函数运行的控制权。假如我们将异步作为主程序的一个分支，那么Callback只是在这个分支上规定了两种结果所导致的不同运行结果，实际上分支依旧是分支，只是结果能影响主程序。而Promise似乎做到了将分支重新归并到主程序，当然根据成功或是失败规定了归并的方式——then或是catch。

## 简单实现
探究Promise的深层逻辑会很有意思，但在此之前不妨自己思考模拟实现一个类Promise对象，下面的23行代码就是我的成果：
```javascript
function MyPromise (executor) {
  let status = 'pending'
  let success = () => {
    if (status === 'pending') {
      status = 'resolve'
    }
  }
  this.then = (resolve = () => {}) => {
    success = () => {
      if (status === 'pending') {
        status = 'resolve'
        resolve()
        success()
      }
    }
    return this
  }
  // 延迟执行，保证then/catch注册处理事件成功
  setTimeout(() => executor(success), 0)
}
new MyPromise(resolve => {
  setTimeout(resolve, 2000)
}).then(() => console.log('success'))
```

虽然看起来非常简陋，但是Promise的核心功能已经在这里实现了，它支持成功回调、链式回调，后续的一些补充和优化我会单开一篇文章来阐述。

# 拓展
JavaScript的发展非常迅速，异步的处理现在已经能非常优雅了，同时，async和await的出现更是让比较难以理解的异步程序变得更符合人们的认知习惯——自上而下的顺序化，如果有兴趣的话，可以将promise、async和await配合使用，我相信用这样的方式编写代码会是一种享受，也会是将来JavaScript处理异步的趋势——至少我已经在工作中将Promise作为常用书写代码了。
