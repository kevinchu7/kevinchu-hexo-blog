---
title: 【置顶】站点公告
index_img: https://static.kevinchu.top/blog/assets/img/cover_011.jpeg
banner_img: https://static.kevinchu.top/blog/assets/img/bg_000.jpg
sticky: 999
archive: false
date: 2023-10-01 10:15:37
updated: 2025-02-15 21:57:10
excerpt: 欢迎来到我的博客😎，这里不定期分享一些技术文章、知识总结和建站经验，祝你玩得开心🥝！
---
<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
.hello {
  position: relative;
  font-family: "Poppins", sans-serif;
  color: #EBCBF7;
  font-size: 90px;
}
.hello:hover {
  text-shadow: 0.05em 0 0 #F9494C, -0.025em -0.05em 0 #4F91F5,
    0.025em 0.05em 0 #57DCB0;
    color: rgba(0, 194, 203, 0.2);
}
.hello:before,
.hello:after {
  content: attr(data-text);
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0.8;
}
.hello:hover::before {
  animation: glitch 650ms infinite;
  clip-path: polygon(0 0, 100% 0, 100% 45%, 0 45%);
  transform: translate(-0.025em, -0.0125em);
}
.hello:hover::after {
  animation: glitch 375ms infinite;
  clip-path: polygon(0 65%, 100% 20%, 100% 100%, 0 70%);
  transform: translate(0.0125em, 0.025em);
}
@keyframes glitch {
  0% {
    color: rgba(236, 34, 37, 0.2);
    text-shadow: 0.05em 0 0 #F9494C, -0.025em -0.05em 0 #4F91F5,
      0.025em 0.05em 0 #57DCB0;
  }
  14% {
    text-shadow: 0.05em 0 0 #F9494C, -0.025em -0.05em 0 #4F91F5,
      0.025em 0.05em 0 #57DCB0;
  }
  15% {
    color: #57DCB0;
    text-shadow: -0.05em -0.025em 0 #F9494C, 0.025em -0.025em 0 #4F91F5,
      -0.05em -0.05em 0 #57DCB0;
  }
  49% {
    text-shadow: -0.05em -0.025em 0 #F9494C, 0.025em -0.025em 0 #4F91F5,
      -0.05em -0.05em 0 #57DCB0;
  }
  50% {
    text-shadow: 0.025em 0.05em 0 #F9494C, -0.025em 0.05em 0 #4F91F5,
      0 -0.05em 0 #57DCB0;
  }
  99% {
    color: #4F91F5;
    text-shadow: 0.025em 0.05em 0 #F9494C, -0.025em 0.05em 0 #4F91F5,
      0 -0.05em 0 #57DCB0;
  }
  100% {
    text-shadow: -0.025em 0 0 #F9494C, -0.025em -0.025em 0 #4F91F5,
      -0.025em -0.05em 0 #57DCB0;
  }
}
</style>
<div class="hello" data-text="HELLO!">HELLO!</div>

# 写在前面

>有人的地方一定有墙。我们都在墙里。没有多少事可以放心到光天化日下去做。
——史铁生《墙下短记》

朋友，你好👋。

欢迎来到我的博客，这里日常分享一些~~技术文章~~、知识总结和建站经验，偶尔记录一点博主的生活碎片。

你可以在[**友链**](https://blog.kevinchu.top/links/)页面申请添加友链，也可以在[**关于**](https://blog.kevinchu.top/about/)页面找到我的联系方式。

欢迎[**留言**](https://blog.kevinchu.top/messagebd/)交流。

祝你玩得开心🥝！


# 本站热门文章
[***1.Hexo博客Fluid主题魔改记录***](https://blog.kevinchu.top/2023/07/17/hexo-theme-fluid-modify/)🔥🔥🔥

[***2.解决npm卡在sill idealTree buildDeps不动问题***](https://blog.kevinchu.top/2024/01/26/npm-sill-idealtree-builddeps/)🔥🔥🔥

[***3.记一次网站CDN流量被恶意盗刷事件***](https://blog.kevinchu.top/2024/07/24/cdn-network-attack/)🔥🔥

[***4.Docker部署Waline(从leancloud迁移)***](https://blog.kevinchu.top/2025/02/15/docker-deploy-waline/)🔥

[***5.Linux离线升级gcc至5.4.0版本***](https://blog.kevinchu.top/2023/02/07/linux-update-gcc-off-line/)🔥


# 一些无聊的公告

## 2025-02-15
{% note info %}
🎉评论系统现已移回国内部署啦！
{% endnote %}
之前就一直想优化下评论系统的加载，但服务部署在境外请求和响应的速度始终堪忧。
现在好了，评论系统加载速度相比以前已经大幅度提升啦~
不过，目前博主的资源和钱包有限🤣，偶尔该有的卡顿还是会有，希望以后可以有机会做更好的优化。

## 2024-05-21 
{% note success %}
🔊和风天气插件失效，导致本站近期加载速度受影响，目前该问题已经修复。
{% endnote %}
![](https://static.kevinchu.top/blog/public/20240521004236.png)
吐槽一下：这波体验有点糟糕哇，虽然说白嫖了很久服务，但是突然停止服务了也不邮件通知下用户么？博主近期比较忙，今天偶然上线了一下才发现问题😭。

## 2024-01-16
{% note info %}
🎉本站的留言板块终于上线！
{% endnote %}
欢迎前来留言，点击[**此处**](https://blog.kevinchu.top/messagebd/)直达。


## 2023-10-21
{% note info %}
🎉“K-MUSIC”现已上线啦！
{% endnote %}
博主大晚上不睡觉竟在搞这个破玩意儿，点击[**此处**](https://music.kevinchu.top)即刻体验。


## 2023-10-09
{% note success %}
🔊评论系统加载失败问题现已修复
{% endnote %}
无事发生。



## 2023-10-07
{% note warning %}
⚠️博客已知问题：评论系统在部分运行商网络下可能会加载失败
{% endnote %}
国庆刚过完，手机联通网络下博客评论系统加载不出来了，试了下果然Vercel不科学上网又无法访问了。目前测试的情况是联通网络是加载不出来评论的，电信是可以的，移动尚不清楚......换个访问更可靠的平台部署也许能解决这样的问题，等我有时间了再来弄吧。



## 2023-10-01
{% note info %}
🎉站点公告正式上线
{% endnote %}
这个人很懒，什么都没有留下。





