---
title: Docker挂载文件宿主机修改后容器内未同步
index_img: https://static.kevinchu.top/blog/assets/img/cover_033.jpeg
archive: false
date: 2025-02-09 19:36:51
updated: 2025-02-09 19:36:51
tags:
    - Docker
    - Linux
categories:
    - 踩坑指南
---

## 1.问题始末

在docker里安装nginx，按照如下方式配置挂载和运行容器：
```BASH
docker run \
--name nginx \
-e HOSTNAME=$(hostname) \
-d -p 81:80 -p 443:443 \
-v /opt/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /opt/nginx/log:/var/log/nginx \
-v /opt/nginx/html:/usr/share/nginx/html \
-v /opt/nginx/cert:/etc/nginx/cert \
-v /opt/nginx/conf.d:/etc/nginx/conf.d \
nginx:latest
```
容器运行正常，
但是，修改宿主机上的```nginx.conf```文件，尝试以下nginx热加载命令重新加载配置：
```BASH
# 容器内nginx重新加载配置
docker exec nginx nginx -s reload
# 或者
docker exec -it nginx service nginx reload
```
结果均未生效，进入容器内部查看对应配置文件，确实未被成功修改。
然而，尝试修改挂载目录html里的文件，使用上述重新加载命令，却能成功生效?!
这说明，修改宿主机上的```nginx.conf```文件未能与容器同步。

去网上搜索了一番，果不其然：如果挂载的是单独的文件，宿主机上修改后不一定能同步到容器中；而挂载目录则没有这种问题，目录中的内容会同步更新。

参考这篇博文：https://cloud.tencent.com/developer/article/1708294



## 2.解决方案

- 创建容器时挂载目录，尽量不要挂载单独的文件

- 重启容器，以使用最新的配置文件（```docker restart <container_id/name>```）
