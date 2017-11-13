---
title: Textarea的maxlength实现
categories:
  - frontend
tags:
  - html
date: 2017-11-13 11:09:21
---

## 实现

常规的`input`有`maxlength`属性直接设置输入框的最大长度，但是对于`textarea`来说因为兼容性的问题，需要自己实现，这里搬运一下网上的方法，并用vue实现一下。

<!-- more -->

```javascript
export default {
  template: `
    <textarea
      v-model="value"
      rows="3" 
      :maxlength="maxlength"
      style="resize:none;overflow:auto;"
      @change="value = value.substring(0, maxlength)"
      @keydown="value = value.substring(0, maxlength)"
      @keyup="value = value.substring(0, maxlength)"></textarea>
  `,
  props: {
    maxlength: {
      type: Number,
      default: 20
    }
  },
  data () {
    return {
      value: ''
    }
  }
}
```

## 分析

这里主要用到了三个事件监听：`onchange`、`onkeydown`、`onkeyup`，用vue实现就是`@change`、`@keydown`、`@keyup`，配合字符串截取函数`substring`获得指定长度的字符串

+ `@keydown`： 按键落下输入监听
+ `@keydown`： 按键弹起输入监听
+ `@change`： 内容变化监听

同时，为了处理IE之类的兼容性，添加maxlength属性。
