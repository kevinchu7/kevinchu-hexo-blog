---
title: Docker如何优雅地部署Nginx
index_img: https://static.kevinchu.top/blog/assets/img/cover_032.jpeg
archive: false
date: 2025-02-10 09:16:34
updated: 2025-02-10 09:16:34
tags:
    - nginx
    - Docker
    - Linux
categories:
    - 软件工具
---


## 1-部署步骤
### 1.1-拉取镜像
选择一个合适的版本（参考[官网](https://hub.docker.com/_/nginx)），推荐使用alpine版本（[什么是Alpine](https://zhuanlan.zhihu.com/p/2022319591649552030)）
```SHELL
docker pull nginx:1.24.0-alpine
```

### 1.2-创建本地目录并初始化

目录结构布局参考如下
```plaintext
/opt/nginx/
├── docker-compose.yml   # 项目入口（如果是通过docker-compose部署）
├── certs/               # 证书（可选）
├── conf/                # 配置文件
│   ├── nginx.conf       # 主配置（初始化后一般不动）
│   └── conf.d/          # 站点/代理配置（日常修改）
├── html/                # 静态资源
└── logs/                # 日志持久化（可选）
```

创建目录
```SHELL
# 创建本地目录
mkdir -p /opt/nginx/{certs,conf,logs}
```

创建临时容器用于初始化（可选，根据需要来）
```SHELL
# 1.创建临时容器（不用run）
docker create --name tmp-nginx nginx:1.24.0-alpine

# 2.拷贝官方默认配置
docker cp tmp-nginx:/etc/nginx/nginx.conf /opt/nginx/conf/
docker cp tmp-nginx:/etc/nginx/conf.d /opt/nginx/conf/
docker cp tmp-nginx:/usr/share/nginx/html /opt/nginx/

# 3.删除临时容器
docker rm tmp-nginx
```


### 1.3-启动容器
```SHELL
docker run -d \
  --name nginx \
  --restart always \
  -p 80:80 \
  -p 443:443 \
  -v /opt/nginx/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
  -v /opt/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
  # 证书，可选
  -v /opt/nginx/certs:/etc/nginx/certs:ro \
  -v /opt/nginx/html:/usr/share/nginx/html:ro \
  # 日志持久化，可选
  -v /opt/nginx/logs:/var/log/nginx \
  -e TZ=Asia/Shanghai \
  nginx:1.24.0-alpine
```


### 1.4-测试

验证nginx服务是否正常
```SHELL
# 默认80，看你配置的
curl 127.0.0.1
```
或者浏览器访问```http://{ip}:{端口}```(端口以你的配置为主，下图示例是81) ，出现nginx经典首页，成功
![](https://static.kevinchu.top/blog/public/20250214150528.png)



## 2-使用docker-compose部署
目录布局和初始化同 [1.2-创建本地目录并初始化](#1-2-创建本地目录并初始化)

在```/opt/nginx```下添加docker-compose.yml，内容参考
```yaml
version: '3.8'

services:
  nginx:
    image: nginx:1.24.0-alpine
    container_name: nginx
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./conf/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./conf/conf.d:/etc/nginx/conf.d:ro
      - ./certs:/etc/nginx/certs:ro
      - ./html:/usr/share/nginx/html:ro
      - ./logs:/var/log/nginx
    environment:
      - TZ=Asia/Shanghai
```

启动容器
```SHELL
docker-compose up -d
```




## 3-配置与使用


### 3.1-配置规范

配置docker部署的nginx，建议遵循这些原则：
- 初始化后尽量不去修改覆盖/opt/nginx/conf/nginx.conf
- 所有站点独立配置，推荐放在/opt/nginx/conf/conf.d/目录下
- 可以一个服务或者域名对应一个.conf配置文件（比如按域名对应命名：example.com.conf、app.example.com.conf）
- 修改配置后必须先检查语法，再热重载

这样做的好处是：可以根据站点、功能分开管理配置，方便维护，降低出错概率。

当然也不是必须😅，根据项目和实际需求来，比如我就想所有配置都在一个nginx.conf里方便拷贝，也是可以的。但要注意的是，以上介绍的挂载方式，是将nginx.conf文件单独挂载，**此处有坑：**在宿主机上修改完这个文件后使用nginx热重载命令很有可能因为inode问题导致容器内部不能同步更新，参考这篇[踩坑文章](https://blog.kevinchu.top/2025/02/09/docker-mount-file-cannot-change/)。


### 3.2-配置模板

常规配置：
```nginx
# /etc/nginx/conf.d/demosite.conf（/opt/nginx/conf.d/demosite.conf）

# ---------- HTTP 自动跳转 HTTPS ----------
server {
    listen 80;
    server_name your-domain.com;

    location / {
        return 301 https://$host$request_uri;
    }
}

# ---------- HTTPS 核心服务 ----------
server {
    listen 443 ssl;
    server_name your-domain.com;

    # 证书和私钥（路径对应宿主机/opt/certs）
    ssl_certificate     /etc/nginx/certs/your-domain.com.pem;
    ssl_certificate_key /etc/nginx/certs/your-domain.com.key;

    # 前端静态文件（路径对应宿主机/opt/nginx/html）
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }


    # 可选：自定义错误页面
    # （示例在html/下新建了error/文件夹，里面放了404.html和50x.html）
    # error_page 404 /error/404.html;
    # error_page 500 502 503 504 /error/50x.html;
    # location = /error/404.html {
    #     internal;  # 可选：禁止直接通过URL访问
    # }
    # location = /error/50x.html {
    #     internal;  # 可选
    # }

    # 可选：API 反向代理（如果有后端）
    # location /api/ {
    #     proxy_pass http://your-backend:8080/;  # 加/后端接收不带/api/，不加则带/api/
    #     proxy_set_header Host $host;
    #     proxy_set_header X-Real-IP $remote_addr;
    #     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #     proxy_set_header X-Forwarded-Proto $scheme;
    # }
}
```

二级域名：
```nginx
# 多二级域名通用模板（也可以按需要分成几个配置文件）
# 主站：your-domain.com / www.your-domain.com
# 后台：admin.your-domain.com
# 应用：app.your-domain.com

# ---------- HTTP 统一跳转 HTTPS ----------
server {
    listen 80;
    server_name your-domain.com www.your-domain.com admin.your-domain.com app.your-domain.com;
    return 301 https://$host$request_uri;
}

# ---------- 主站：your-domain.com + www.your-domain.com 【主站】 ----------
server {
    listen 443 ssl;
    server_name your-domain.com www.your-domain.com;

    ssl_certificate     /etc/nginx/certs/your-domain.com.pem;
    ssl_certificate_key /etc/nginx/certs/your-domain.com.key;

    root /usr/share/nginx/html/main;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}

# ---------- 后台站点：admin.your-domain.com 【二级域名分站】 ----------
server {
    listen 443 ssl;
    server_name admin.your-domain.com;

    ssl_certificate     /etc/nginx/certs/your-domain.com.pem;
    ssl_certificate_key /etc/nginx/certs/your-domain.com.key;

    root /usr/share/nginx/html/admin;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}

# ---------- 应用站点：app.your-domain.com 【二级域名分站】 ----------
server {
    listen 443 ssl;
    server_name app.your-domain.com;

    ssl_certificate     /etc/nginx/certs/your-domain.com.pem;
    ssl_certificate_key /etc/nginx/certs/your-domain.com.key;

    root /usr/share/nginx/html/app;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

二级目录：
```nginx
# ---------- HTTP 80 强制跳转 HTTPS ----------
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    return 301 https://$host$request_uri;
}

# ---------- HTTPS 主服务 + 多二级目录 ----------
server {
    listen 443 ssl;
    server_name your-domain.com www.your-domain.com;

    # SSL 证书
    ssl_certificate     /etc/nginx/certs/your-domain.com.pem;
    ssl_certificate_key /etc/nginx/certs/your-domain.com.key;

    # 根站点根目录
    root /usr/share/nginx/html/main;
    index index.html;

    # 根路径 / （默认主站 SPA）
    location / {
        try_files $uri $uri/ /index.html;
    }

    # 二级目录 /app
    location /app/ {
        alias /usr/share/nginx/html/app/;
        try_files $uri $uri/ /app/index.html;
    }

    # 二级目录 /user
    location /user/ {
        alias /usr/share/nginx/html/user/;
        try_files $uri $uri/ /user/index.html;
    }

    # 二级目录 /admin
    location /admin/ {
        alias /usr/share/nginx/html/admin/;
        try_files $uri $uri/ /admin/index.html;
    }
}
```

### 3.3-日志管理
#### 3.3.1 方案一：挂载到宿主机目录+logrotate 管理
适用于单机开发/生产，需要精细的轮转策略（按天轮转、压缩、自定义保留策略）时，将nginx日志挂载到宿主机目录，使用系统级工具logrotate进行日志管理（大多数Linux发行版已预装logrotate，若未安装，```sudo yum install logrotate -y```安装即可）。

创建或编辑 /etc/logrotate.d/nginx：
```bash
sudo vi /etc/logrotate.d/nginx
```
写入：
```conf
# 宿主机Nginx容器日志轮转配置
/data/nginx/logs/*.log {
    daily                   # 按天切割
    maxsize 100M            # 超过100M立即切割
    rotate 7                # 保留7份历史日志
    compress                # 切割后gzip压缩
    delaycompress           # 下一次轮转再压缩，不占用瞬时IO
    missingok               # 日志不存在不报错
    notifempty              # 空日志不切割
    create 0644 nginx nginx  # 新建日志权限+属主
    sharedscripts           # 所有日志只执行一次后置脚本
    postrotate
        # 向容器内nginx发送信号，重新打开日志文件（核心！）
        docker exec nginx-log nginx -s reopen > /dev/null 2>&1
    endscript
}
```
模拟调试（不真实切割）：

```bash
logrotate -d /etc/logrotate.d/nginx
```
强制手动切割
```bash
logrotate -f /etc/logrotate.d/nginx
```
logrotate 自带系统定时任务```cron.daily```，每天自动执行，无需手动加定时；
当然也可以自定义执行时间：
```bash
# 备份原定时任务
mv /etc/cron.daily/logrotate /etc/cron.daily/logrotate.bak

# 创建新定时任务
crontab -e
```
写入规则，例如：
```bash
# 每天凌晨0点整切割
0 0 * * * /usr/sbin/logrotate /etc/logrotate.d/nginx >/dev/null 2>&1
``` 

查看是否写入成功：
```bash
crontab -l
```

#### 3.3.2 方案二（推荐）：stdout/stderr输出+docker内置日志限制配置
docker默认使用json-file日志驱动，将容器stdout和stderr的输出以JSON格式逐行存储到宿主机磁盘，路径为```/var/lib/docker/containers/<容器ID>/<容器ID>-json.log```，将nginx日志配置输出到stdout和stderr，而非直接写入容器内部文件，这样docker可以自动捕获并管理这些日志，实现统一轮转。

**nginx最新官方镜像默认已将日志输出到stdout和stderr**，**覆盖行为请注意**：需要注意的是，当使用```-v```或```--mount```将宿主机的目录挂载到容器的```/var/log/nginx```目录时，会覆盖容器内原有的目录内容。这会导致官方的符号链接被覆盖，日志会写入到宿主机挂载的目录中，从而无法再通过```docker logs```查看。

手动配置日志输出至stdout、stderr，编辑nginx.conf：
```nginx
# 在 http 块内添加/修改以下两个配置

# 将错误日志发送到标准错误
error_log /dev/stderr;

http {
    # ... 其他配置 ...

    # 将访问日志发送到标准输出
    access_log /dev/stdout;
    
    # ... 其他配置 ...
}
```
使用docker日志驱动管理日志（介绍2种配置方式）:
1.启动时配置
运行时添加配置：
```bash
docker run -d \
  --name my-nginx \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  nginx:stable
```
docker-compose配置：
在docker-compose.yml中为服务添加```logging```字段：
```yaml
services:
  web:
    image: nginx:stable
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```
2.全局配置（可选，对所有新容器生效）
编辑```/etc/docker/daemon.json```：
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```
重启docker生效
```bash
systemctl restart docker
```
参数说明
| 参数 | 作用 |
| :-- | :-- |
| `--log-driver=local` | Docker 官方推荐生产驱动，压缩存储、性能最高 |
| `max-size=100m` | 单个日志文件最大 100MB，自动切割 |
| `max-file=5` | 最多保留 5 份日志，自动清理旧日志，防磁盘爆满 |



#### 3.3.3 方案三：ELK/EFK集中式日志管理

适用于大型生产系统，通过搭建集中式日志平台，把多台服务器、docker容器、服务日志统一收集、清洗、存储、检索、可视化、告警，替代挨个登录服务器看日志。其中常见的架构方案有传统的ELK（Elasticsearch+Logstash+Kibana）以及当前主流的EFK（Elasticsearch+Fluentd+Kibana）。

ELK简介：
Elasticsearch —— 分布式搜索和分析引擎，负责存储和索引日志数据
Logstash —— 服务器端数据处理管道，负责采集、过滤、解析、转换日志
Kibana —— 数据可视化平台，负责查询、展示、分析日志

部署和使用细节暂略；


### 3.3-常用命令


测试配置：
```SHELL
docker exec nginx nginx -t
```

热重启：
```SHELL
docker exec nginx nginx -s reload
```

查看日志
docker容器原始日志（配置了日志输出stdout、stderr）：
```SHELL
# 实时查看日志
docker logs -f nginx

# 查看最近100行
docker logs --tail 100 nginx

# 查看1小时内日志
docker logs --since 1h nginx
```
若是挂载到宿主机目录的日志：
```SHELL
tail -f /opt/nginx/logs/access.log
```

重启容器：
```SHELL
docker restart nginx
```

进入容器：
```SHELL
docker exec -it nginx /bin/bash
```

