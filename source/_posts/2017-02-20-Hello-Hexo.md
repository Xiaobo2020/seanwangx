---
title: Hello Hexo
categories:
  - other
tags:
  - Hexo
date: 2017-02-20 20:23:29
---

我相信，技术水平的提升不会是一步登天，而在于平时点点滴滴积累的质变，第一篇博客就以Hexo这个博客框架来作为主题吧。

<!-- more -->

# 准备
使用Hexo搭建博客很方便，但是也需要一点准备操作，这里需要两样工具：
+ Git
+ Node

在安装了上面两个工具以后，要安装hexo-cli来创建你的博客生成项目
```bash
npm install -g hexo-cli
hexo init your-blog-sit
cd your-blog-site
npm install
```
这样你就完成了你的本地博客生成项目了

# 常用指令

## init
这条指令在上文中已经出现过了，即为创建你的博客生成项目
```bash
hexo init [folder]
```

## new
该指令主要用于写作时候文章的创建
```bash
hexo new [layout] <title>
```
其中<code>layout</code>为文章类型，常用的为以下两种：
+ posts: 待发布文章，生成在/source/_posts文件夹中
+ drafts: 待发布文章的草稿，生成在/source/_drafts文件夹中

## publish
发表草稿
```bash
hexo publish [layout] <filename>
```

## generate
生成用于在博客中显示的静态html文件
```bash
hexo generate
```
支持参数：
+ -d: 生成静态文件后立即部署网站
+ -w: 监视文件改动

## server
启动本地服务器，默认localhost:4000中查看博客
```bash
hexo server
```
支持参数：
+ -p: 重设端口
+ -s: 只使用静态文件

## deploy
部署网站
```bash
hexo deploy
```
支持参数：
+ -g: 部署之前先生成静态文件

## clean
清除缓存数据以及静态文件
```bash
hexo clean
```

# 结语
本文只是记录了一下Hexo的一些基本操作，更多详细的内容见[hexo中文官网](https://hexo.io/zh-cn/)。
另外如果你想要在不同电脑上都可以更新你的博客，那么你可以把你的本地项目保存在github上，之后就只要在需要的时候从github上下载下来就能开始更新你的博客了。
