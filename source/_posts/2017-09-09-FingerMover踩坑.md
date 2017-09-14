---
title: finger-mover踩坑记录
categories:
  - frontend
tags:
  - FingerMover
  - Vue
date: 2017-09-09 17:32:06
---

# 前言
在vue新项目中，尝试使用了[finger-mover](https://github.com/HcySunYang/finger-mover)作为移动端滑动的解决方案，因为是初次在vue中使用`finger-mover`，所以不可避免的踩了点坑，这里略做记录。

<!-- more -->

# 场景描述
使用的是`finger-mover`的一个插件——`simulation-scroll-y`，用于纵向的模拟滚动。项目中用了一个`loadMore(无限滚动)`的功能，加载没有问题，但是对于首次加载的数据条目超过屏幕的宽度的时候，会发现屏幕的滚动出现了问题——无法滑动查看末尾的数据。

# 问题解决
无限滚动的配置很简单，一般创建在`created`的生命周期之中：

```javascript
created () {
  this.fm = new Fmover({
    el: '#scroll-box',
    plugins: [
      simulationScrollY({
        loadMore: {
          distance: 0,
          onLoadMore: function () {
            setTimeout(() => {
              // 调用 loadEnd() 方法重置加载更多操作
              this.loadEnd()
            }, 2000)
          }
        }
      })
    ]
  })
}
```

当加载更多触发后，即`onLoadMore`事件触发后，`simulation-scroll-y`会锁住该事件，以防止上一次网络请求未完成后的重复触发，这时候需要`loadEnd()`结束，并且重新计算滚动方式。

来看看首屏问题——数据超出屏幕，但是屏幕无法滚动，分析可知道是vue中数据绑定之后但是`simulation-scroll-y`没有计算高度重新刷新滚动。解决方案很简单，使用的是`refreshSize`这个方法和`updated`这个生命周期钩子。

```javascript
updated () {
  this.$nextTick(() => {
    this.fm[0].refreshSize()
  })
}
```

这里的`this.fm[0]`就是`simulation-scroll-y`实例。

# 结束
多看文档，多分析问题。
