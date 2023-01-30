---
title: 解决idea启动tomcat时控制台及日志出现中文乱码问题
index_img: https://static.kevinchu.top/blog/assets/img/cover_038.jpeg
date: 2023-01-30 13:45:12
tags:
    - IDEA
    - tomcat
categories:
    - 踩坑指南
---
## 问题
idea配置本地tomcat服务器，启动服务器时，控制台打印日志出现中文乱码的问题：

![](https://static.kevinchu.top/blog/public/20230130140426.png)

![](https://static.kevinchu.top/blog/public/20230130141007.png)

## 解决

1.修改idea settings配置

![](https://static.kevinchu.top/blog/public/20230130142600.png)

保存后重启下服务器，看到idea的server控制台日志中文已能正常显示，但是tomcat catalina log仍有中文乱码问题：

![](https://static.kevinchu.top/blog/public/20230130142912.png)

![](https://static.kevinchu.top/blog/public/20230130143047.png)


2.修改tomcat配置文件

打开并编辑本地tomcat目录下conf文件夹中的logging.properties文件：

![](https://static.kevinchu.top/blog/public/20230130154003.png)

将图示位置改为```GBK```：

![](https://static.kevinchu.top/blog/public/20230130154335.png)

保存，并重启服务器，可以看到tomcat catalina log中文已能正常显示：

![](https://static.kevinchu.top/blog/public/20230130154757.png)

问题解决。