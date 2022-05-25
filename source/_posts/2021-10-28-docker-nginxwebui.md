---
title: Docker部署NginxWebUI
index_img: https://static.kevinchu.top/blog/assets/img/cover_008.jpeg
date: 2021-10-28 17:27:27
tags:
    - Docker
    - nginx
categories:
    - 软件工具
---
## 1 关于NginxWebUI
NginxWebUI是一款方便实用的nginx 网页配置工具，可以使用 WebUI 配置 Nginx 的各项功能，包括端口转发，反向代理，ssl 证书配置，负载均衡等，最终生成「nginx.conf」配置文件并覆盖目标配置文件，完成 nginx 的功能配置。
>NginxWebUI相关地址
项目地址：https://gitee.com/cym1102/nginxWebUI
官方网站：https://nginxwebui.gitee.io

## 2 Docker部署NginxWebUI
- 拉取镜像文件
```shell
docker pull cym1102/nginxwebui:latest
```
- 运行
```shell
docker run -itd --restart=always --name=nginxWebUI -v /home/nginxWebUI:/home/nginxWebUI -e BOOT_OPTIONS="--server.port=8083" --privileged=true --net=host  cym1102/nginxwebui:latest /bin/bash
```
- 初始化
初始化界面：
![](https://static.kevinchu.top/blog/public/nginxwebui-01.png)
菜单界面：
![](https://static.kevinchu.top/blog/public/nginxwebui-02.png)
