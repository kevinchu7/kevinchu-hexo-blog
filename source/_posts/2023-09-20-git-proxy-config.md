---
title: Git设置http全局代理
index_img: https://static.kevinchu.top/blog/assets/img/cover_014.jpeg
archive: false
date: 2023-09-20 15:38:30
updated: 2023-09-20 15:38:30
tags:
    - Git
categories:
    - 软件工具
---

>Github加速必备，配合代理工具食用更佳🚀。

## 1.设置全局http代理和https代理
```shell
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```


## 2.取消全局代理
```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```