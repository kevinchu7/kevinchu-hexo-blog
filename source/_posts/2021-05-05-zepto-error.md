---
title: 解决使用zepto报错
index_img: https://static.kevinchu.top/blog/assets/img/cover_007.jpeg
date: 2021-05-05 09:28:12
update: 2021-05-05 09:28:12
tags:
    - Javascript
categories:
    - 踩坑指南
---
## 1 问题
>zepto.js，是JQuery的移动端版本，一个轻量级的JQuery。文档:<https://zeptojs.com/>。

使用zepto时，控制台报错：$(...).animateCss is not a function，如图：
![](https://static.kevinchu.top/blog/public/zepto-error.png)

## 2 解决
zepto.min.js默认只加载一部分模块，其中缺少animate的模块。
可以去<https://github.com/madrobby/zepto/blob/master/src/fx.js>，将需要的内容引入或追加到zepto.min.js中即可。