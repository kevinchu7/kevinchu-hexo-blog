---
title: Vercel部署Waline评论系统
index_img: https://static.kevinchu.top/blog/assets/img/cover_044.jpeg
archive: false
date: 2023-07-17 10:09:03
tags:
    - Waline
    - Vercel
    - LeanCloud
categories:
    - 博客建站
---

>一直以来本站的评论系统使用的是基于github issues的[utterances](https://utteranc.es/) ，其优点是完全免费、足够轻量，缺点也是很显然的，必须使用github账号登录后才可以评论，加载的速度有亿点儿慢。这段时间国内github的加载速度越发感人，我觉得是时候换一个评论系统体验体验了。
---
# 1.简介

## 1.1 Vercel
Vercel是一个基于云的开源平台，可以帮助开发者在不需要管理服务器的情况下，轻松地将网站和应用程序部署到各种基础架构之上。它提供了一个强大的、可扩展的平台，可以让开发者在不同的平台和环境中部署和管理他们的代码。官网：https://vercel.com/ 。

## 1.2 Waline
Waline是一款基于 Valine 衍生的简洁、安全的评论系统，官网：https://waline.js.org/ 。

## 1.3 LeanCloud
LeanCloud（原 AVOS Cloud） 是针对移动应用的一站式云端服务，专注于为应用开发者提供工具和平台。提供包括LeanStorage 数据存储、LeanMessage 通信服务、LeanAnalytics 统计分析、LeanModules 拓展模块等四大类型的后端云服务，加速应用开发。官网(国际版)：https://leancloud.app/ 。


# 2.Vercel部署Waline评论系统

## 2.1 LeanCloud设置（数据库）
1.注册登录LeanCloud[控制台](https://console.leancloud.app/apps)

2.点击左上角创建应用，选择免费的开发版
![](https://static.kevinchu.top/blog/public/20230717143823.png)

3.进入新建的应用，点击左侧的```设置->应用凭证```，记录下```APP ID```、```APP Key``` 和```Master Key```三个参数


## 2.2 Vercel部署Waline (服务端)
1.注册登录[Vercel](https://vercel.com/)

2.点击[**此处**](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fwalinejs%2Fwaline%2Ftree%2Fmain%2Fexample)进行部署：

（1）认证github，输入项目名称，点击create
![](https://static.kevinchu.top/blog/public/20230717151007.png) 

（2）等待片刻
![](https://static.kevinchu.top/blog/public/20230717151156.png)

（3）部署完成，点击```Go to Dashboard```，跳转至控制台
![](https://static.kevinchu.top/blog/public/20230717151337.png)

（4）点击顶部的```Settings->Environment Variables```进入环境变量配置页，并配置三个环境变量```LEAN_ID```，```LEAN_KEY```和 ```LEAN_MASTER_KEY```，它们的值分别对应上一步在LeanCloud中获得的```APP ID```，```APP KEY```，```Master Key```，记得点击右下角```Save```
![](https://static.kevinchu.top/blog/public/20230717162135.png)

（5）环境变量配置完成后，点击顶部```Deployments```，进行```Redeploy```，使配置生效
![](https://static.kevinchu.top/blog/public/20230717165226.png)

（6）重新部署完成后，点击```Visit```即可访问Waline服务端地址
![](https://static.kevinchu.top/blog/public/20230717173617.png)

（7）绑定域名（这一步可选）。点击项目```Settings->Domains```，在此处填写要绑定的域名，并在域名服务商配置中添加cname类型解析记录，可完成域名绑定
![](https://static.kevinchu.top/blog/public/20230717174726.png)
>PS:关于绑定域名再补充一下，由于```vercel.app```域名被DNS污染，最近国内Waline加载吃力，经常出现访问不了的情况，这个问题可以通过使用有备案域名直接转发Vercel DNS Server地址来改善，需要添加cname类型解析记录，记录值为```cname.vercel-dns.com```，参考这篇[博客](https://lisenhui.cn/blog/use-custom-domain-active-vercel-waline.html)。


## 2.3 访问服务端
在Vercel的Waline项目中点击```Visit```，即可跳转至Waline的评论系统地址，在该页面点击```Login```，弹框页面则为服务端的登录地址，可在此注册和登录
![](https://static.kevinchu.top/blog/public/20230717212320.png)
![](https://static.kevinchu.top/blog/public/20230717213021.png)

亦可以通过```评论系统地址/ui```或是登录过后点击自己的头像进入管理页面。
![](https://static.kevinchu.top/blog/public/20230717213327.png)


# 3.在HTML中引入Waline
在你的网页中进行如下设置:
1.导入 Waline 样式 ```https://unpkg.com/@waline/client@v2/dist/waline.css```。

创建```<script>```标签使用来自```https://unpkg.com/@waline/client@v2/dist/waline.mjs```的```init()```函数初始化，并传入必要的```el```与 ```serverURL```选项。

- ```el```选项是Waline渲染使用的元素，你可以设置一个字符串形式的CSS选择器或者一个HTMLElement对象。
- ```serverURL```是服务端的地址，即上一步获取到的值。

```HTML
<head>
  <!-- ... -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/@waline/client@v2/dist/waline.css"
  />
  <!-- ... -->
</head>
<body>
  <!-- ... -->
  <div id="waline"></div>
  <script type="module">
    import { init } from 'https://unpkg.com/@waline/client@v2/dist/waline.mjs';

    init({
      el: '#waline',
      serverURL: 'https://your-domain.vercel.app',
    });
  </script>
</body>
```

# 4.在Hexo博客Fluid主题中使用Waline

修改主题配置文件：

1.修改```comments```配置项
```YML
comments:
    enable: true
    type: waline
```
2.启用```waline```配置项
```YML
waline:
  serverURL: 'your-server-url'
  path: window.location.pathname
  meta: ['nick', 'mail', 'link']
  requiredMeta: ['nick']
  lang: 'zh-CN'
  emoji: ['https://cdn.jsdelivr.net/gh/walinejs/emojis/weibo']
  dark: 'html[data-user-color-scheme="dark"]'
  wordLimit: 0
  pageSize: 10
  locale: 
    placeholder: '哈，快找个位置随便坐~'
```

使用成功
![](https://static.kevinchu.top/blog/public/20230718021511.png)

