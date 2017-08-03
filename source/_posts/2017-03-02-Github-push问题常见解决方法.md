---
title: Github push问题常见解决方法
categories:
  - git
tags:
  - Git
date: 2017-03-02 23:52:50
---

## 描述
在部署hexo的时候偶然遇到了一个问题，截取了一下关键错误信息如下。


```shell
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.
```

<!-- more -->

## 解决
简单翻译可以知道是无法读取远程仓库，同时信息给了提示确保拥有权限或者确保关联的远程仓库是存在的。首先就是确保远程仓库存在，并且地址输入正确，其次，使用SSH的情况下，将git@github.com加入到信任列表中，这里可以尝试使用如下命令：

```shell
ssh -T git@github.com
```


之后就是一路确认到底了。另外有兴趣的可以看一下[官方文档](https://help.github.com/articles/connecting-to-github-with-ssh/)