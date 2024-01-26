---
title: 解决npm卡在sill idealTree buildDeps不动问题
index_img: https://static.kevinchu.top/blog/assets/img/cover_022.jpeg
archive: false
date: 2024-01-26 14:14:48
updated: 2024-01-26 14:14:48
tags:
    - npm
categories:
    - 踩坑指南
---

## 1.问题

最近不知道是代理软件开多了还是什么其他原因，`npm install`忽然有一天就不行了，不管怎么试，一直卡在`sill idealTree buildDeps`这个第一步一动不动。

百度了一下，说原因是什么默认环境设在境外了，访问速度慢所以卡住，解决方法也是简单的切数据源，切成淘宝镜像源（ https://registry.npm.taobao.org ）。但其实我查看了一下我原本就已经是这个镜像源了😂，对我没什么效果。

后来是通过清缓存，切数据源，这一系列组合流程尝试下来，终于解决了。


## 2.解决方法

依次做以下尝试，

- 找到用户文件夹下的.npmrc文件，并删除（注意windows路径一般是在：`C:\Users\{用户名}\.npmrc`，不是node目录下的）

- 清除缓存，执行`npm cache verify`

- 切换镜像源，例如`npm config set registry https://registry.npmmirror.com`

以上三步操作过后，再测试`npm install`命令，已能正常执行。
