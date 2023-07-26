---
title: MySQL存储emoji表情踩坑
index_img: https://static.kevinchu.top/blog/assets/img/cover_014.jpeg
date: 2022-03-26 15:54:21
updated: 2022-03-26 15:54:21
tags:
    - MySQL
categories:
    - 踩坑指南
---
## 问题
uft-8编码可能2个字节、3个字节、4个字节，MySql的uft-8只支持3字节的数据，而移动端的表情数据是4字节的字符。如果直接采用utf-8编码的数据库中插入表情数据，Java程序将报错：
![](https://static.kevinchu.top/blog/public/mysql-emoji-error-01.png)
## 解决
调整需要的字段、表或数据库编码，更改编码为utf8mb4。
修改后便能成功存入：
![](https://static.kevinchu.top/blog/public/mysql-emoji-error-02.png)
>utf8mb4编码是utf8编码的超集，兼容utf8，并且能存储4字节的表情字符。
采用utf8mb4的好处是：存储与获取数据的时候，不用考虑编码和解码的问题。