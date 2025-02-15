---
title: Docker安装与配置（CentOS）
index_img: https://static.kevinchu.top/blog/assets/img/cover_026.jpeg
date: 2021-06-20 15:12:10
updated: 2025-02-12 19:20:10
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
PS: docker-ce、docker-ce-cli和containerd.io是Docker架构中的关键组件，三者关系：

**(1)docker-ce（Docker Community Edition，即Docker社区版，核心引擎）**

Docker引擎是容器的核心管理组件，包含以下核心模块：
- Docker守护进程（dockerd）：常驻后台的服务，负责监听API请求（如创建/删除容器）。
- REST API：提供标准接口供客户端（如CLI或其他工具）与守护进程交互。
- 镜像构建工具：支持通过Dockerfile构建镜像。

docker-ce本身不直接操作容器，而是依赖containerd.io处理底层容器操作（如启动容器、管理存储卷等）。

**(2)docker-ce-cli（Docker的命令行工具）**
提供docker命令（如docker run、docker build等），是用户与Docker引擎交互的入口。
它通过REST API向docker-ce发送指令（如创建容器等），并接收返回结果。

CLI工具本身不依赖containerd.io，但需要docker-ce正常运行才能执行命令（例如：如果Docker引擎未启动，docker ps会直接报错）。

**(3)containerd.io（容器运行时工具）**
负责容器的底层操作，是Docker引擎的底层依赖，包括：
- 镜像管理（拉取、存储、挂载镜像）。
- 容器生命周期管理（创建、启动、停止、删除容器）。
- 存储卷和网络配置（与操作系统内核交互）。

containerd.io是通用的容器运行时工具，Kubernetes等其他工具也可直接使用它（无需Docker）。

**(4)协作流程**
```plaintext
用户输入命令
   │
   ↓
docker-ce-cli（CLI工具） → 通过 REST API → docker-ce（引擎）
                                       │
                                       ↓
                                containerd.io（容器运行时）
                                       │
                                       ↓
                                     runc（实际创建容器进程）

```

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
示例配置：
```JSON
{
    "registry-mirrors":["https://mirror.ccs.tencentyun.com"],
    "log-driver":"json-file",
    "log-opts": {"max-size":"500m", "max-file":"5"}
}
```
上述以腾讯云镜像源为例，参考[文档](https://cloud.tencent.com/document/product/213/8623)

日志相关选项：
max-size：为文件最大大小
max-file：为最多保持几个文件

除了json-file还支持很多logging driver：
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
- 在线下载对应版本docker-compose文件进行安装
(建议点击[此处](https://github.com/docker/compose/releases)去github查看版本号)
```SHELL
sudo curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
- 也可以去[github](https://github.com/docker/compose/releases)下载文件上传到服务器上进行安装
找到带有```docker-compose-linux-x86_64```的版本，把这个包下载下来上传到服务器/usr/local/bin目录下面，再将其名称修改为```docker-compose```
```SHELL
mv docker-compose-linux-x86_64 docker-compose
```

- 添加可执行权限
```SHELL
sudo chmod +x /usr/local/bin/docker-compose
```

- 测试docker-compose安装
```SHELL
docker-compose --version
```

## 6 修改Docker默认存储位置（可选）
>有很多方法修改docker的默认存储位置，可以参考博文：
>- https://blog.csdn.net/BigData_Mining/article/details/104921479 
>- https://blog.csdn.net/weixin_44756008/article/details/143593515

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
- 把/var/lib/docker目录下面的文件复制到新目录下面
```SHELL
cp -R /var/lib/docker/* /home/work/docker_root
```
- 修改docker配置文件(```/etc/docker/daemon.json```)
在文件中添加或修改以下内容，使之指向新的目录：
```
"data-root": "/home/work/docker_root"
```
- 启动服务
```SHELL
systemctsystemctl status docker
```
可用```docker info```查看Docker Root Dir是否修改成功
- 确认稳定无误后，可以删除原先路径内容
```SHELL
sudo rm -rf /var/lib/docker
```

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
