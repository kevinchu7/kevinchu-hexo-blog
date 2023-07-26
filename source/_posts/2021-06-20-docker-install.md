---
title: Docker安装与配置（CentOS）
index_img: https://static.kevinchu.top/blog/assets/img/cover_026.jpeg
date: 2021-06-20 15:12:10
updated: 2021-06-20 15:12:10
tags:
    - Docker
    - 服务器
    - Linux
categories:
    - 软件工具
---
## 1 删除历史版本
如果之前系统上安装过旧版本的Docker，使用下面命令卸载：
```SHELL
sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-selinux \
                    docker-engine-selinux \
                    docker-engine \
                    docker-ce
```


## 2 安装Docker

### 2.1 安装yum工具
```SHELL
sudo yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```


### 2.2 配置本地镜像源
```SHELL
# 官方
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# 阿里
sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 2.3 利用yum安装Docker
```SHELL
sudo yum install docker-ce docker-ce-cli containerd.io
```
>docker-ce、docker-ce-cli、containerd.io三者区别官方解释：
>containerd.io - daemon to interface with the OS API (in this case, LXC - Linux Containers), essentially decouples Docker from the OS, also provides container services for non-Docker container managers
docker-ce - Docker daemon, this is the part that does all the management work, requires the other two on Linux
docker-ce-cli - CLI tools to control the daemon, you can install them on their own if you want to control a remote Docker daemon
>
>containerd.io - 与 OS API 接口的守护进程（在本例中为 LXC - Linux Containers），本质上将 Docker 与 OS 分离，还为非 Docker 容器管理器提供容器服务
docker-ce - Docker 守护进程，这是完成所有管理工作的部分，在 Linux 上需要另外两个
docker-ce-cli - 用于控制守护进程的 CLI 工具，如果您想控制远程 Docker 守护进程，可以自行安装它们


## 3 启动Docker

Docker应用会需要用到各种端口，需要逐一修改防火墙。
可以通过以下命令关闭防火墙:
```SHELL
# 关闭
systemctl stop firewalld
# 禁止开机启动防火墙
systemctl disable firewalld
# 查看防火墙状态
systemctl status firewalld
```

```SHELL
# 启动 docker
sudo systemctl start docker
# 停止 docker
sudo systemctl stop docker
# 重启 docker
sudo systemctl restart docker
# 设置开机启动
sudo systemctl enable docker
# 查看 docker 状态
sudo systemctl status docker
# 查看 docker 内容器的运行状态
sudo docker stats
# 查看 docker 概要信息
sudo docker info
# 查看 docker 帮助文档
sudo docker --help
```


## 4 配置Docker
可以通过修改daemon配置文件```/etc/docker/daemon.json```来配置镜像加速、日志文件等
```
vi /etc/docker/daemon.json

# 如果不存在 
mkdir -p /etc/docker/ 
touch /etc/docker/daemon.json
```

```JSON
{
    "registry-mirrors":["https://pwg3dujk.mirror.aliyuncs.com"],
    "log-driver":"json-file",
    "log-opts": {"max-size":"500m", "max-file":"5"}
}
```
>附：中国科学技术大学（LUG@USTC）的开源镜像：https://docker.mirrors.ustc.edu.cn 和网易的开源镜像：http://hub-mirror.c.163.com


日志相关：

max-size 为文件最大大小
max-file 为最多保持几个文件

除了 json-file 还支持很多 logging driver：

日志驱动|描述
-|-  
none | 容器没有日志可用，docker logs 什么都不返回
local | 日志以自定义格式存储，设计这种格式的目的是将开销降到最低。
syslog | 将日志消息写入 syslog 工具，syslog 守护程序必须在主机上运行。
journald | 将日志消息写入 journald，journald 守护程序必须在主机上运行。
gelf | 将日志消息写入 Graylog Extended Log Format (GELF) 终端，例如 Graylog 或 Logstash。
fluentd	| 将日志消息写入 fluentd（forward input），fluentd | 守护程序必须在主机上运行。
awslogs	| 将日志消息写入 Amazon CloudWatch Logs。
splunk	| 使用HTTP事件收集器将日志消息写入splunk。
etwlogs	| 将日志消息写为 Windows 的 Event Tracing 事件，仅在Windows平台上可用。
gcplogs	| 将日志消息写入 Google Cloud Platform (GCP) Logging。
logentries | 将日志消息写入 Rapid7 Logentries。
json-file | 日志格式化为 JSON，这是 Docker 默认的日志驱动程序。

重新加载配置：
```SHELL
# 重新加载某个服务的配置文件
sudo systemctl daemon-reload
# 重新启动 docker
sudo systemctl restart docker
```

## 5 安装docker-compose
- 下载对应版本docker-compose文件进行安装
```SHELL
sudo curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
- github访问太慢可以用daocloud下载
```SHELL
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

- 添加可执行权限
```SHELL
sudo chmod +x /usr/local/bin/docker-compose
```

- 测试docker-compose安装
```SHELL
docker-compose --version
```

## 6 修改Docker默认存储位置
>三种方法修改docker的默认存储位置，参考博文：https://blog.csdn.net/BigData_Mining/article/details/104921479

### 6.1 直接复制
- 停掉docker服务
```SHELL
systemctl stop docker
```

- 创建新的docker目录，如```home/work/docker_root```
>df -h 可查看磁盘目录空间大小

```SHELL
mkdir -p /home/work/docker_root
```
- 迁移/var/lib/docker目录下面的文件到 /home/docker/lib下面
```SHELL
cp -R /var/lib/docker/* /home/work/docker_root
```
- 修改docker配置(```/etc/systemd/system/docker.service.d/docker-options.conf```)
将文件中的–-data--root改为新的目录地址



### 6.2 软链接方式实现

- 查看docker存放的具体位置（默认情况下为：/var/lib/docker）
```SHELL
sudo docker info | grep "Docker Root Dir"
```

- 停掉docker服务
```SHELL
systemctl stop docker或者service docker stop
```

- 然后移动整个/var/lib/docker目录到目的路径
```SHELL
mv /var/lib/docker  /home/work/docker_root

ln -s /home/work/docker_root  /var/lib/docker
```
这时候启动Docker时发现存储目录依旧是/var/lib/docker，但是实际上是存储在数据盘的，可以在数据盘上看到容量变化。
