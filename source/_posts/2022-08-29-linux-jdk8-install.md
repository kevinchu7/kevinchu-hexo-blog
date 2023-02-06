---
title: CentOS7安装配置JDK1.8(压缩包方式)
index_img: https://static.kevinchu.top/blog/assets/img/cover_034.jpeg
date: 2022-08-29 09:46:04
tags:
    - Linux
    - Java
    - 服务器
categories:
    - 软件工具
---
### 1 下载安装

#### 1.1 下载JDk1.8压缩包
JDK 1.8 for Linux 官方下载地址：https://www.oracle.com/java/technologies/downloads/#java8

下载后ftp上传至服务器

#### 1.2 创建目录并解压

- 在/usr目录下创建java文件夹
```
mkdir /usr/java
```

- 将压缩包移动到/usr/java，并解压
```
mv jdk-8u131-linux-x64.tar.gz /usr/java


tar -zxvf jdk-8u131-linux-x64.tar.gz
```

- 删除压缩包

```
rm -rf jdk-8u131-linux-x64.tar.gz
```


### 2 配置

#### 2.1 配置环境变量

修改/etc目录下的profile文件
```
/etc/profile
```
(注：若此时提示：```-bash: vim: 未找到命令。```说明缺少vim，先安装vim：```yum -y install vim*```)

在profile文件尾行追加以下内容
```
export JAVA_HOME=/usr/java/jdk1.8.0_131
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

刷新使配置生效
```
source /etc/profile 
```

测试是否配置成功
```
java -version
```
显示
```
[root@VM-0-7-centos jdk1.8.0_131]# java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```
配置完成。