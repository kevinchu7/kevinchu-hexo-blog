---
title: Docker部署Jenkins
index_img: https://static.kevinchu.top/blog/assets/img/cover_013.jpeg
date: 2022-03-05 09:30:13
update: 2022-03-05 09:30:13
tags:
    - Docker
    - Jenkins
categories:
    - 软件工具
---
# 1 关于Jenkins
[Jenkins官网](https://www.jenkins.io/)
>Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。


# 2 Docker部署Jenkins
## 2.1 拉取镜像
```shell
docker pull jenkins/jenkins
```

## 2.2 创建Jenkins挂载目录并授权
```shell
mkdir -p /var/docker_mounts/jenkins_mount
```
```shell
chmod 777 /var/docker_mounts/jenkins_mount
```
(这一步是为方便对容器内的配置文件进行修改。）


## 2.3 创建并启动Jenkins容器
```shell
docker run -d -p 9998:8080 -p 9999:50000 -v /var/docker_mounts/jenkins_mount:/var/jenkins_home -v /etc/localtime:/etc/localtime --name jenkins jenkins/jenkins
```
分析：
- docker run：创建/运行容器
- -d：后台守护进程形式运行容器
- --name jenkins：给容器定义别名叫jenkins
- -p 9998:8080：映射容器端口([缩主机端口]:[容器端口])
- -u root：指定系统用户运行该容器，这里我指定root用户
- -v /etc/localtime:/etc/localtime：缩主机系统时间与容器系统时间绑定
- -v /var/docker_mounts/jenkins_mount:/var/jenkins_home：缩主机目录与容器目录共享绑定
- jenkins/jenkins：通过镜像名创建该容器

(可以使用```docker ps -l```查看容器是否启动成功。）


## 2.4 配置镜像加速
进入挂载映射的目录
```cd /var/docker_mounts/jenkins_mount```
修改hudson.model.UpdateCenter.xml文件内容
```vi hudson.model.UpdateCenter.xml```
将默认url修改为清华大学官方镜像
```https://updates.jenkins.io/update-center.json``` $\longrightarrow$ ```https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json```
![](https://static.kevinchu.top/blog/public/Jenkins-01.png)


## 2.5 访问Jenkins页面进行后续配置
访问IP+:9998(Jenkins容器映射端口号）进行后续安装和配置。
![](https://static.kevinchu.top/blog/public/Jenkins-02.png)
至此，完成。



参考:
https://www.cnblogs.com/fuzongle/p/12834080.html
https://blog.csdn.net/zczhi_java/article/details/111152493