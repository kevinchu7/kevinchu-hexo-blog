---
title: Docker安装redis
index_img: https://static.kevinchu.top/blog/assets/img/cover_033.jpeg
date: 2022-07-21 11:11:22
updated: 2022-07-21 11:11:22
tags:
    - Docker
    - redis
    - Linux
    - 服务器
categories:
    - 软件工具
---

### 1 拉取官方镜像
```
docker pull redis
```


### 2 创建挂载目录
```
mkdir /home/redis/conf
```

### 3 创建redis.conf文件
进入挂载目录后
创建redis.conf配置文件
```
touch /home/redis/conf/redis.conf
```
或者官网下载
```
wget http://download.redis.io/redis-stable/redis.conf
```
授权
```
chmod 777 redis.conf
```

### 4 修改配置文件

修改默认配置信息
```
vi /docker-data/redis/redis.conf
```
```
# bind 127.0.0.1 # 这行要注释掉，解除本地连接限制
protected-mode no # 默认yes，如果设置为yes，则只允许在本机的回环连接，其他机器无法连接。
daemonize no # 默认no 为不守护进程模式，docker部署不需要改为yes，docker run -d本身就是后台启动，不然会冲突
requirepass 123456 # 设置密码
appendonly yes # 持久化
```

### 5 docker创建redis容器并启动

```
docker run --name redis \
-p 6379:6379 \
-v /home/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /home/redis:/data \
-d redis redis-server /etc/redis/redis.conf --appendonly yes
```

- -p 6379:6379：端口映射，前面是宿主机，后面是容器。
- –name redis：指定该容器名称。
- -v 挂载文件或目录：前面是宿主机，后面是容器。
- -d redis redis-server - /etc/redis/redis.conf：表示后台启动redis，以配置文件启动redis，加载容器内的conf文件。
- appendonly yes：开启redis 持久化。
