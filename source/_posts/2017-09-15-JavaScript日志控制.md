---
title: JavaScript日志控制
categories:
  - frontend
tags:
  - JavaScript
  - Log
date: 2017-09-15 22:32:01
---

# 前言
前端调试有很多方法，并且各人有各人的习惯，比如通过浏览器的devTool打断点进行debug调试，或者清新脱俗的alert，不过我最常使用的还是console.log进行日志输出进行代码的调试。但是随着前端工程化发展，日志管理也变得非常有必要，所以，我希望能对于开发环境喝生产环境的日志进行区分，或者说分级控制。看了下目前的一些JS日志工具，可以说比较丰富了，然而还是想要自己尝试写一下这个日志控制。

<!-- more -->

# 功能分析
理想中，日志控制器应该有以下几个最基本的功能：

+ 准确性
+ 可分级
+ 可移植

## 准确性
作为日志的输出，信息正确永远是需要保证的，所以这里只是对原生的console进行了一下封装，并没有改变API，并且根据级别不同调用不同的方法如`console.error()`、`console.warn()`、`console.log()`等。

## 可分级
现在的开发是区分环境的——开发环境（dev）以及生产环境（prod），所以为了配合不同环境的输出日志信息不同的目的，日志的输出应该是分级的，参照Java的log4j，这里将日志的级别分为六个级别，等级从高到低排序：

1. OFF: 不输出任何日志信息
2. ERROR: **错误**级别
3. WARN: **警告**级别
4. INFO: **提示**级别
5. DEBUG: **调试**级别
6. ALL: 所有日志均输出

日志的输出级别如果设定为`WARN`，那么`WARN`以下级别（`INFO`和`DEBUG`）的日志就不会进行输出了。通过分级以及环境的区分，我们可以很好实现在不同环境下日志的输出管理。

## 可移植
作为一个工具，日志控制器需要能很好适应现今的不同框架，所以，封装为单独的模块是一个比较好的做法，这样就能在需要的地方引入，便于开发使用。

# 功能实现
这里关于日志控制器`Log4JS`的实现采用的是es5传统创建对象的方式，没有采用es6中`class`的方式。

## 创建等级字典
由于有分级的要求，所以创建一个等级字典很有必要。

```javascript
// 级别名字典
const TYPE = {
  OFF: 'OFF',
  ERROR: 'ERROR',
  WARN: 'WARN',
  INFO: 'INFO',
  DEBUG: 'DEBUG',
  ALL: 'ALL'
}

// 级别顺序字典
const LEVEL = {
  [TYPE.OFF]: 0,
  [TYPE.ERROR]: 1,
  [TYPE.WARN]: 2,
  [TYPE.INFO]: 3,
  [TYPE.DEBUG]: 4,
  [TYPE.ALL]: 5,
}
```

顺便提一下，常量`LEVEL`中尝试性使用了一下es6对于对象的扩展——属性名的书写方式。

## 创建Log4JS对象
预想中，初始化时可以接受一个等级，作为输出日志级别的控制。

```javascript
function Log4JS (lvNm = TYPE.WARN) {
  lvNm = TYPE[String(lvNm).toUpperCase()] || TYPE.WARN
  let level = LEVEL[lvNm]
  console.log(`Log4JS Level: ${lvNm}`)
}
```

当创建`Log4JS`实例的时候，应该就会设定好日志的输出级别名，以及对应的判定级别。

因为不想每次创建实例都创建重复的打印函数，所以将日志输出方法放在原型上。

```javascript
/**
  * 错误级别日志
  */
Log4JS.prototype.error = function () {
  if (LEVEL[TYPE.ERROR] > level) return
  console.error(`[${TYPE.ERROR}]`, ...arguments)
}
/**
  * 警告级别日志
  */
Log4JS.prototype.warn = function () {
  if (LEVEL[TYPE.WARN] > level) return
  console.warn(`[${TYPE.WARN}]`, ...arguments)
}

/**
  * 提示级别日志
  */
Log4JS.prototype.info = function () {
  if (LEVEL[TYPE.INFO] > level) return
  console.info(`[${TYPE.INFO}]`, ...arguments)
}

/**
  * 调试级别日志
  */
Log4JS.prototype.debug = function () {
  if (LEVEL[TYPE.DEBUG] > level) return
  console.log(`[${TYPE.DEBUG}]`, ...arguments)
}
```

# Log4JS的使用
为了配合环境更好地使用极简日志控制器，所以一般入口会进行一些修改，做法是创建一个`Logger.js`：

```javascript
import Log4JS from './Log4JS'
import config from '../../config'

const lv = (process.env.NODE_ENV === 'production' ? config.build.logLv : config.dev.logLv)

export default new Log4JS(lv)
```

这是取自Vue项目中的实际使用，如果是其他框架或者结构，可自行修改适配。

# 结尾
认真分析自己写的这个日志控制器，可以看到功能实现非常的简单，而且有一个比较突出的问题，那就是由于是对原生`console`的封装，导致无法追踪具体文件位置，这是比较麻烦的地方，目前正在尝试研究`Error`对象，希望通过`Error`对象的内部信息完善这款日志控制器。

[仓库地址](https://github.com/SeanWangx/Log4JS)
