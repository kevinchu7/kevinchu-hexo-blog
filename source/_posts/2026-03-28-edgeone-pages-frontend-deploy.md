---
title: EdgeOne Pages零配置部署前端应用
index_img: https://static.kevinchu.top/blog/assets/img/cover_036.jpeg
archive: false
date: 2026-03-28 19:17:39
updated: 2026-03-28 19:17:39
tags:
        
categories:
---

## 一、前言

最近搭了一个基于VuePress的文档站，以往前端项目部署基本都是放在服务器或者各类托管平台上，再额外配个CDN加速，步骤略显繁琐，成本不低。这次偶然尝试了腾讯云 [**EdgeOne Pages**](https://cloud.tencent.com/document/product/1552/127366)，感觉体验还蛮不错，有免费额度，自带基于EdgeOne的边缘网络全球加速，几乎零配置，不用看教程随便点点就能傻瓜式完成部署，我愿称之为**国产Vercel**🤣。

本文简单介绍和记录一下EdgeOne Pages部署前端应用的基础步骤。

## 二、EdgeOne Pages部署前端应用

### 2.1 创建项目

登录[**控制台**](https://console.cloud.tencent.com/edgeone/pages)，点击**服务总览** >> **Pages** >> **创建项目**，当前支持三种方式创建：

- 通过导入git仓库创建：关联git仓库提供商，从git仓库内导入代码创建网站；
- 通过模板创建：可选从 EdgeOne 预制的模板快速创建一个网站；
- 通过上传文件创建：自行上传当前的项目代码到 EdgeOne，根据上传的代码内容来部署站点；

以下主要介绍通过关联git仓库导入代码来进行构建和部署。


### 2.2 连接git仓库

点击导入git仓库，当前支持以下几类git提供商：
![](https://static.kevinchu.top/blog/public/20260508155917.png)
选择git仓库进行登录、授权、关联，完成后即可看到导入成功的仓库。
注意：授权Repository access时，最好选择"Only select repositories"，不要默认"All repositories"。


### 2.3 自定义构建参数

在导入的git仓库中选择需要构建的项目，按照提示填写项目名称、分支、加速区域、构建命令（可在package.json的scripts中查看）和输出目录（build完的输出目录，如vue cli一般默认的dist）等参数信息，如图：    
![](https://static.kevinchu.top/blog/public/1778277264933.jpg)


### 2.4 测试部署

直接点击**开始部署**，在下方能够查看构建日志的实时输出，等待数秒后，若出现和Vercel类似的经典礼花特性，部署成功！
![](https://static.kevinchu.top/blog/public/1778277291202.jpg)
点击预览，可以通过临时的预览链接看到项目的部署效果（通过正式链接访问需要绑定域名）。

若此次构建未能成功，可以在项目详情左侧菜单找到**项目设置** >> **构建部署配置**，编辑构建参数以再次尝试。

至此，通过git仓库导入代码构建和部署前端应用的基本步骤就完成了，关联的仓库主分支每次提交过后都会触发自动构建从而更新站点，不需要再去配置github action、Jenkins等CI工具，当然也支持通过手动或者通过钩子来触发部署，可以说是很方便了😋。


### 2.5 绑定域名和配置证书

可以在项目详情左侧菜单的**域名管理**里添加自定义域名并配置HTTPS访问证书。

添加域名支持一键配置CNAME记录到DNS解析里以校验状态；配置证书可以在此页面申请时长3月的免费证书（平台支持[**证书托管**](https://console.cloud.tencent.com/ssl/dsc/hosting)），也可以选择购买或者绑定已有的SSL证书。


## 三、总结

EdgeOne Pages作为腾讯云边缘安全加速平台EO推出的服务产品，相比Vercel、Netlify、Cloudflare Pages等主流平台，有着本土化支持友好、腾讯云生态深度集成、性价比高等优势，**截止目前来说**😅，免费版额度充足，配置简单，对于以国内为主的网站及应用的部署场景，可以作为一个不错的选择。

更多功能使用与配置方式，可以参考[**EdgeOne Pages官方文档**](https://cloud.tencent.com/document/product/1552/127366)。
