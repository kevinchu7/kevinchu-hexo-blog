---
title: Linux防火墙常用命令
index_img: https://static.kevinchu.top/blog/assets/img/cover_041.jpeg
date: 2023-02-08 14:06:40
updated: 2023-02-08 14:06:40
tags: 
    - Linux
    - 服务器
categories:
    - 学习记录
---
### 1.开启、关闭、禁用防火墙

```shell
# 1.启动防火墙
systemctl start firewalld

# 2.关闭防火墙
systemctl stop firewalld

# 3.检查防火墙状态
systemctl status firewalld

# 4.设置开机启用防火墙
systemctl enable firewalld.service

# 5.设置开机禁用防火墙
systemctl disable firewalld.service
```


### 2.使用firewall-cmd配置端口

```shell
# 1.查看防火墙状态
firewall-cmd --state

# 2.查看开放的端口
firewall-cmd --list-ports

# 3.开启防火墙端口(命令含义：–zone #作用域;–add-port=9200/tcp #添加端口，格式为：端口/通讯协议;–permanent #永久生效，没有此参数重启后失效）
firewall-cmd --zone=public --add-port=9200/tcp --permanent

# 4.关闭防火墙端口
firewall-cmd --zone=public --remove-port=9200/tcp --permanent

# 5.重新加载配置(修改端口后必须重新加载配置才会生效)
firewall-cmd --reload
```