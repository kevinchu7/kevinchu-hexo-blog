---
title: Vercel部署Twikoo评论系统
index_img: https://static.kevinchu.top/blog/assets/img/cover_012.jpeg
archive: false
date: 2023-09-19 10:36:02
updated: 2023-09-19 10:36:02
tags:
    - Twikoo
    - vercel
categories:
    - 博客建站
---

# 1 前言

前段时间，本站将评论系统从[utterances](https://utteranc.es/)替换为了[waline](https://waline.js.org/)，部署过程参考这篇[博客](https://blog.kevinchu.top/2023/07/17/vercel-deploy-waline/)。使用了一阵子后发现本站所使用的主题对waline目前的兼容性有待提高，常常会在移动端触发各种各样的bug。于是，这次看中了[Twikoo](https://twikoo.js.org/)评论系统，再试一次。


# 2 Twikoo简介
[Twikoo](https://twikoo.js.org/)是一个简洁、安全、免费的静态网站评论系统。

与waline相比，
优势：支持KaTeX公式；
不足：不支持IE，私有部署需要配置图床。


# 3 Vercel部署Twikoo评论系统

## 3.1 创建MongoDB Cloud数据库

**（1）添加User**
登录[MongoDB Cloud](https://cloud.mongodb.com/)官网（没账号需要先[注册](https://www.mongodb.com/cloud/atlas/register)），在`Database Access`页面点击`Add New Database User`创建数据库用户，`Authentication Method`选`Password`，在`Password Authentication`下设置数据库用户名和密码，用户名和密码可包含数字和大小写字母，不能包含特殊符号。点击`Database User Privileges`下方的`Add Built In Role`，`elect Role`选择`Atlas Admin`，最后点击`Add User`
![](https://static.kevinchu.top/blog/public/20230919134741.png)

**（2）添加Network Access**
在`Network Access`页面点击`Add IP Address`，`Access List Entry`输入`0.0.0.0/0`（允许所有IP地址的连接），点击`Confirm`
![](https://static.kevinchu.top/blog/public/20230919134854.png)

**(3) 获取数据库连接字符串**
在`Database`页面点击`Connect`，连接方式选择`Drivers`，记录数据库连接字符串，将连接字符串中的`<password>`修改为刚刚创建的数据库密码
![](https://static.kevinchu.top/blog/public/20230919135337.png)

## 3.2 Vercel部署Twikoo

**(1) 一键部署**
点击[**此处**](https://vercel.com/import/project?template=https://github.com/twikoojs/twikoo/tree/main/src/server/vercel-min)，一键部署到Vercel

**(2) 配置环境变量**
选择新建的Twikoo项目，进入`Settings -> Environment Variables`，添加环境变量`MONGODB_URI`，值为前面的MongoDB数据库连接字符串（注意替换`<Password>`）。
![](https://static.kevinchu.top/blog/public/20230919145739.png)

**(3) redeploy**
进入`Deployments`，进行`Redeploy`
![](https://static.kevinchu.top/blog/public/20230919145835.png)

等待重新部署完成后，进入`Project`，查看`Overview`或者点击`visit`，可以看见“Twikoo 云函数运行正常”的提示，部署成功
![](https://static.kevinchu.top/blog/public/20230919150108.png)

**(4) 绑定域名**
进入`Settings -> Domains`，在此处填写要绑定的域名，并在域名服务商配置中添加cname类型解析记录，可参考[Vercel部署Waline](https://blog.kevinchu.top/2023/07/17/vercel-deploy-waline/)绑定域名过程


# 4 在Hexo Fluid主题中使用Twikoo（配置Twikoo前端）

## 4.1 修改主题配置文件
编辑主题配置文件，修改Twikoo评论相关配置，envId为部署的访问地址（或绑定域名），基于vercel部署`region`和`path`不必填写

```yml
# Twikoo
# 基于腾讯云开发
# Based on Tencent CloudBase
# See: https://twikoo.js.org
twikoo:
  envId: {your twikoo url}
  region: 
  path: 
```

## 4.2 初始化Twikoo

启动Hexo，如果配置没有问题，在开启评论的页面可以看见Twikoo的评论界面，点击配置图案进行初始化配置
![](https://static.kevinchu.top/blog/public/20230919170437.png)


# 5 总结

一番折腾下来，我发现本主题对Twikoo的兼容性似乎更好，但是体验下来还是存在亿点不足。通过Vercel部署的Twikoo不能上传图片，还需要自己配置图床（且支持的图床也不多），访问速度和流畅性也一般。此外，还是感觉Waline的ui更好看一些。于是，果断换回了Waline。Twikoo你好，Twikoo再见！😭