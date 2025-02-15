---
title: Docker部署Nginx
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

>相关文章：[Docker安装与配置（CentOS）](https://blog.kevinchu.top/2021/06/20/docker-install/)


## 1.拉取镜像
```BASH
docker pull nginx:latest
```

## 2.启动容器
```BASH
docker run --name nginx -d -p 81:80 nginx:latest
```

## 3.复制配置文件至宿主机
```BASH
# 创建目录
mkdir /opt/nginx

# 复制容器里的配置文件至宿主机
docker cp nginx:/etc/nginx/nginx.conf /opt/nginx/
docker cp nginx:/etc/nginx/conf.d /opt/nginx/
docker cp nginx:/usr/share/nginx/html /opt/nginx/

# 创建日志、证书挂载目录
mkdir /opt/nginx/log /opt/nginx/cert
```

## 4.停止并移除先前容器
```BASH
docker stop nginx
docker rm nginx
```

## 5.启动容器映射挂载目录
>注意：1.本例中nginx使用了81端口，如需调整请自行修改；2.启动时传递了环境变量HOSTNAME，有需要可以```$HOSTNAME```获取。
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

## 6.测试

访问```http://{ip}:81```(端口以你的配置为主) ，出现nginx经典首页，部署成功！

![](https://static.kevinchu.top/blog/public/20250214150528.png)


## 7.注意事项

### 7.1重新加载配置

更改完宿主机上的nginx映射的内容后，执行以下命令，可以重新加载配置

```BASH
docker exec nginx nginx -s reload
# 或者
docker exec -it nginx service nginx reload
```
**注意:此处有坑！**如果单独挂载了```nginx.conf```文件，在宿主机上修改完此配置文件后，使用上述热加载命令恐无法生效！要想使配置成功生效，需要重启容器：
```BASH
docker restart nginx
```
参考这篇[踩坑文章](https://blog.kevinchu.top/2025/02/09/docker-mount-file-cannot-change/)。

### 7.2.IP细节

nginx配置反向代理时，往往需要填写其他服务的访问地址（ip+端口）。

一般情况下，使用服务器所在内网地址，即宿主机eth0网口（或者wlan0）分配的ip，加上服务端口即可，可使用```ifconfig```或者```ip addr```查询；

而基于docker部署的nginx，在默认的桥接网络模式下，容器与宿主机之间通信是通过搭建新的虚拟网桥docker0来实现的，可以使用上述查询命令查询宿主机在docker0网桥下的所属ip，也可以```docker inspect <container_id/name>```来查看容器的网关地址("Gateway")，使用该ip+端口也可以正常访问服务。

因此，在配置反向代理时，可以代理到宿主机eth0或wlan0分配的ip+服务端口，也可以代理到docker0分配的ip+服务端口（推荐使用前者）。

