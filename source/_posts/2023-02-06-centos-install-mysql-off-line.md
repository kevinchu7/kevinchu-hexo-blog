---
title: CentOS7离线安装MySQL5.7
index_img: https://static.kevinchu.top/blog/assets/img/cover_039.jpeg
date: 2023-02-06 13:46:56
tags:
    - MySQL
    - Linux
    - 服务器
categories:
    - 软件工具
---
### 1.安装前检查是否有残余MySQL文件

- 检查rpm方式安装残留

```
# 查包名
rpm -qa|grep -i mysql
# 删除命令
rpm -e –nodeps 包名
# 查找mysql相关目录
find / -name mysql
# 删除相关目录(依次删除)
rm -rf "目录文件路径"
# 删除 /etc/my.cnf
rm -rf /etc/my.cnf
# 删除/var/log/mysqld.log（如果不删除这个文件，会导致新安装的mysql无法生存新密码，导致无法登陆
rm -rf /var/log/mysqld.log
# 查看mysql用户及用户组
more /etc/passwd | grep mysql
more /etc/group | grep mysql
more /etc/shadow | grep mysql
# 删除mysql用户及用户组
userdel mysql
groupdel mysql
# 检查是否有mariadb
rpm -qa | grep mariadb
# 如果有则卸载
rpm -e --nodeps mariadb-libs
rpm -e --nodeps mariadb-devel-5.5.65-1.el7.x86_64
```

- 检查yum方式安装残留

```
#查看已安装的mysql
rpm -qa | grep -i mysql
#卸载mysql
yum remove 文件名
```

### 2.下载MySQL5.7安装包

官网下载MySQL5.7离线安装包：https://downloads.mysql.com/archives/community/

![](https://static.kevinchu.top/blog/public/20230206142706.png)


### 3.离线安装MySQL

创建目录
```
mkdir /usr/local/software/mysql 
```

上传MySQL安装包(mysql-5.7.38-1.el7.x86_64.rpm-bundle.tar)至该目录下，并解压
```
tar -xvf mysql-5.7.38-1.el7.x86_64.rpm-bundle.tar
```

安装
```
# 1.安装依赖 
rpm -ivh --nodeps --force mysql-community-common-5.7.38-1.el7.x86_64.rpm
# 2.安装libs
rpm -ivh --nodeps --force mysql-community-libs-5.7.38-1.el7.x86_64.rpm 
# 3.安装客户端 
rpm -ivh --nodeps --force mysql-community-client-5.7.38-1.el7.x86_64.rpm 
# 4.安装服务 
rpm -ivh --nodeps --force mysql-community-server-5.7.38-1.el7.x86_64.rpm 
```

### 4.修改密码

启动服务，获取临时密码

```
# 启动服务
systemctl start mysqld 或者 service mysqld start
# 获取密码
grep 'temporary password' /var/log/mysqld.log
```

![](https://static.kevinchu.top/blog/public/20230206222853.png)

使用临时密码登录，然后修改密码

```
# 输入刚刚查到的临时密码(输入的时候密码会隐藏，不显示)，登录MySQL
mysql -uroot -p
# 登录以后，设置新密码
set password for root@localhost = password('新的密码');
```

注意：如果密码级别与默认的级别要求不符时候会报```Your password does not satisfy the current policy requirements```, 此时可以修改级别与最小的默认密码位数,然后再重新设置密码：
```
set global validate_password_policy=0;
set global validate_password_length=4;
```

### 5.开启远程访问权限

默认情况下，MySQL是不支持远程登录的，所以需要设置开启，并且刷新权限缓存。
MySQL开启远程访问权限有以下两种方法：

- 授权法

```
# user为用户名，ip为可访问的IP地址，password为密码
GRANT ALL PRIVILEGES ON *.* TO 'user'@'ip' IDENTIFIED BY 'password' WITH GRANT OPTION;

# 例如：想要允许用户myuser从ip为192.168.1.3的主机连接到MySQL服务器，并使用mypassword作为密码
grant all privileges on *.* to 'myuser'@'192.168.1.3' identified by 'mypassword' with grant option;

# IP如果填'%'，表示任何IP都可以访问，建议不要用%，可替换成具体IP地址
grant all privileges on *.* to 'root'@'%' identified by '你的密码' with grant option;

# 修改完记得刷新权限
flush privileges;
```

- 改表法

```
use mysql; 
update user set host = '%' where user = 'root'; 
```

### 6.修改字符编码

MySQL安装后默认不支持中文，需要修改编码。
修改配置文件```/etc/my.cnf```，在相关节点（没有则自行添加）下添加编码配置，注意位置如下：
```
[mysqld]
character-set-server=utf8
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```
![](https://static.kevinchu.top/blog/public/20230206234752.png)

```
# 重启服务，查看字符编码集
systemctl restart mysqld
mysql -uroot -p
show variables like 'character%';
```

![](https://static.kevinchu.top/blog/public/20230206234933.png)

### 7.开放防火墙3306端口

```
# 查看防火墙开放端口
firewall-cmd --list-all
# 防火墙开放3306端口
# permanent为永久生效，没有此参数 服务器重启后配置失效
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
```

### 8.其他配置

```
# 1.设置安全选项：
mysql_secure_installation

# 2.开启/关闭MySQL
systemctl start/stop mysqld
service mysqld start/stop

# 3.重启MySQL
systemctl restart mysqld 

# 4.查看MySQL运行状态
systemctl status mysqld 
service mysqld status

# 5.设置开机启动
systemctl enable mysqld 

# 6.关闭开机启动
systemctl disable mysqld 

# 7.其他默认配置文件路径：
# 配置文件：
/etc/my.cnf 
# 数据目录
/var/lib/mysql
# 日志文件：
/var/log/mysqld.log 
# 服务启动脚本：
/var/lib/mysql/mysql.sock
# 其他文件：
/var/run/mysqld/mysqld.pid

# 8.查看版本
select version();
```


(本文参考自博客：https://blog.csdn.net/qq_43430759/article/details/126057897)