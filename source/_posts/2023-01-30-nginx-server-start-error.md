---
title: nginx启动报错"/var/run/nginx/nginx.pid" failed"
index_img: https://static.kevinchu.top/blog/assets/img/cover_037.jpeg
date: 2023-01-30 10:10:07
updated: 2023-01-30 10:10:07
tags:
    - nginx
    - Linux
    - 服务器
categories:
    - 踩坑指南
---

## 问题
重启虚拟机后，再次重启nginx报错:
```open() "/var/run/nginx/nginx.pid" failed (2: No such file or directory)```

## 解决
>1. 进入 cd /usr/local/nginx/conf/ 目录，编辑配置文件nginx.conf ；
>2. 在配置文件中有个注释的地方： #pid        logs/nginx.pid;
>3. 将注释放开，并修改为：pid    /usr/local/nginx/logs/nginx.pid;
>4. 在 /usr/local/nginx 目录下创建 logs 目录：mkdir /usr/local/nginx/logs
>5. 启动nginx服务：/usr/local/nginx/sbin/nginx