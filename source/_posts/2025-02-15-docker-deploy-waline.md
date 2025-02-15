---
title: Docker部署Waline(从leancloud迁移)
index_img: https://static.kevinchu.top/blog/assets/img/cover_026.jpeg
archive: false
date: 2025-02-15 12:30:20
updated: 2025-02-15 12:30:20
tags:
categories:
---

>相关文章：
>- [Vercel部署Waline评论系统](https://blog.kevinchu.top/2023/07/17/vercel-deploy-waline/)
>- [Docker安装与配置（CentOS）](https://blog.kevinchu.top/2021/06/20/docker-install/)

## 1.前言

本站使用的waline评论系统是基于Vercel和leancloud部署的，由于服务在境外，长期以来访问速度堪忧，经常评论加载不出来。为优化评论系统的加载速度，博主决定将waline转移回国内服务器部署。

参考[waline官方文档](https://waline.js.org/guide/deploy/vps.html)，基于docker部署waline是一种比较方便的方式，于是选用此方式部署。


## 2.docker部署waline

### 2.1 拉取代码，构建镜像

```BASH
# 服务器选择合适位置，拉取代码
git clone https://github.com/walinejs/waline.git

# 构建镜像
cd waline
docker build -t lizheming/waline -f packages/server/Dockerfile .
```

校验：```docker images```查看镜像```lizheming/waline:latest```是否存在。


### 2.2 初始化数据库

waline本地部署支持多种数据库，参考官方文档：[多数据库服务支持](https://waline.js.org/guide/database.html)。

这里，因为我的服务器配置有限，于是选择体量较小的SQLite作为数据库。
下载[waline.sqlite](https://github.com/walinejs/waline/blob/main/assets/waline.sqlite)，上传至服务器合适位置（如```/opt/waline/db```）

如果选择的是其他数据库，需要根据文档进行相应的初始化。

### 2.3 配置docker-compose，启动容器

waline环境变量配置参考：[服务端环境变量](https://waline.js.org/reference/server/env.html)

以下是docker-compose.yml的参考写法，需注意宿主机挂载目录要是waline.sqlite数据库文件所在的目录：
```YML
# docker-compose.yml
version: '3'

services:
  waline:
    container_name: waline
    image: lizheming/waline:latest
    restart: always
    ports:
      - 8360:8360
    volumes:
      - ${PWD}/db:/app/data
    environment:
      TZ: 'Asia/Shanghai'
      # SQLite  
      SQLITE_PATH: '/app/data'
      JWT_TOKEN: '任意字符串'
      # 基本信息
      SITE_NAME: "网站名称"
      SITE_URL: '网站URL'
      AUTHOR_EMAIL: ''
      # 邮件通知
      SMTP_SERVICE: 'QQ'
      SMTP_HOST: 'smtp.qq.com'
      SMTP_PORT: '465'
      SMTP_USER: ''
      SMTP_PASS: ''
      SMTP_SECURE: ''
      SENDER_NAME: ''
      SENDER_EMAIL: ''
```
将yml文件上传到服务器合适位置（如```/opt/waline```），然后```docker-compose up -d```拉起容器。


访问8360端口（云服务器需要配置防火墙或安全组，开放该端口），出现如下页面：

![](https://static.kevinchu.top/blog/public/20250214083911.png)

安装成功！

### 2.4 反向代理

若想要不加端口访问，或者有绑定域名需求的，需要nginx进行反向代理。

以下是监听443端口（https）代理的server参考配置：
```NGINX
    server {
        listen 443 ssl;
        server_name your_server_name;
        ssl_certificate  your_ssl_certificate.pem;
        ssl_certificate_key your_ssl_certificate.key;

        location / {
            proxy_pass http://your_ip:8360;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

    }
```

# 3.从leancloud迁移数据

waline评论系统后台是带有数据导入/导出功能的（登录后台->左上角管理菜单->导入导出），

但是，从别的后台导出的数据文件，如果想要导入到与之前有不同数据库结构的后台，结果很有可能报错，会出现用户与评论关联数据丢失的问题。

如果想要解决类似上面这样的数据导入出错的问题或者说你的Vercerl服务已经挂了（以前的评论后台无法访问），其实也比较简单，咱可以直接从leancloud的数据库里拿啊🤣（leancloud应该不太会挂）。


以SQLite数据库为例，因为数据表id字段与leancloud数据库里的objectId类型匹配不上，需要稍作调整，具体操作：

（1）下载一个新的[waline.sqlite](https://github.com/walinejs/waline/blob/main/assets/waline.sqlite)，使用数据库编辑软件（如Navicat）打开该文件，可以看到有三张初始空表（wl_Comment、wl_Counter、wl_Users）：

![](https://static.kevinchu.top/blog/public/20250215172252.png)

编辑表格wl_Users，临时新增一个```objectId```字段，类型text；
编辑表格wl_Comment，临时新增一个```userObjectId```字段，类型text。

（2）登录leancloud，进入waline的应用，点击数据存储->结构化数据，找到三张对应的表（Comment、Counter、Users），分别点进去点右上角下载查询结果，以csv的形式把三张表数据下载下来：
![](https://static.kevinchu.top/blog/public/20250215174612.png)

然后，使用数据库软件（如Navicat）将三张表的数据导入进waline.sqlite对应的表里。

注意导入users数据时候，```objectId```数据要导入到前面临时新增的```objectId```字段里去；
注意导入comment数据时候，```userId```数据要导入到前面临时新增的```userObjectId```字段里去。

接着，执行sql，将comment表里的user_id与与user表id关联起来：
```SQL
UPDATE wl_Comment 
SET user_id = ( SELECT id FROM wl_Users WHERE objectId = wl_Comment.userObjectId LIMIT 1 ) 
WHERE
	EXISTS ( SELECT 1 FROM wl_Users WHERE objectId = wl_Comment.userObjectId );
```

再然后，删除上一步中wl_Users、wl_Comment两张表里临时新增的字段，save文件。

（3）将该waline.sqlite上传至服务器，替换掉原有的waline.sqlite文件，然后```docker restart waline```重启容器。

至此，waline数据迁移完成🎉。如果是博客使用的话，不要忘了去博客配置文件里更新评论系统的服务url。