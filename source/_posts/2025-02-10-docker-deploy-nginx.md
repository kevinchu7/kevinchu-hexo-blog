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
├── certs/               # 证书
├── conf/                # 配置文件
│   ├── nginx.conf       # 主配置（初始化后一般不动）
│   └── conf.d/          # 站点/代理配置（日常修改）
├── html/                # 静态资源
└── logs/                # 日志
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
  -v /opt/nginx/certs:/etc/nginx/certs:ro \
  -v /opt/nginx/html:/usr/share/nginx/html:ro \
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
- 初始化后尽量不去动/opt/nginx/conf/nginx.conf
- 所有站点独立配置，放在/opt/nginx/conf/conf.d/目录下
- 一个服务或者域名对应一个.conf配置文件（比如按域名对应命名：example.com.conf、app.example.com.conf）
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






### 3.3-常用命令


测试配置：
```SHELL
docker exec nginx nginx -t
```

热重启：
```SHELL
docker exec nginx nginx -s reload
```

查看日志：
```SHELL
docker logs -f nginx
```

重启容器：
```SHELL
docker restart nginx
```

进入容器：
```SHELL
docker exec -it nginx /bin/bash
```

