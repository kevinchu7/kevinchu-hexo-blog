---
title: CentOS7安装Qt5.9.1
index_img: https://static.kevinchu.top/blog/assets/img/cover_032.jpeg
date: 2022-07-10 23:15:51
updated: 2022-07-10 23:15:51
tags: 
    - Linux
    - 服务器
    - Qt
    - C/C++
categories:
    - 软件工具
---
### 1 下载
官网下载Qt5.9.1，地址：https://iso.mirrors.ustc.edu.cn/qtproject/archive/qt/5.9/5.9.1/qt-opensource-linux-x64-5.9.1.run

### 2 安装

#### 2.1 安装需要的依赖
```
yum -y install mesa-libGL-devel mesa-libGLU-devel freeglut-devel
```

#### 2.2 执行安装程序


将qt-opensource-linux-x64-5.9.1.run上传至服务器目标目录下，增加可执行权限，然后运行
```
chmod a+x qt-opensource-linux-x64-5.9.1.run

./qt-opensource-linux-x64-5.9.1.run
```
弹出安装界面如图，无脑默认下一步（需要准备个邮箱用于注册账号）

![](https://static.kevinchu.top/blog/public/qt-install.png)

(注：**此步需要图形界面支持**！若是使用ssh登录的，可以安装完图形界面，采用VNC方式登录后执行安装程序。云服务器图形界面安装参考：https://cloud.tencent.com/developer/article/1758509)

安装完成可打开软件如图

![](https://static.kevinchu.top/blog/public/qt-welcome.png)

### 3 配置

完成安装后，编辑/etc/profile，末尾追加
```
export PATH=/opt/Qt5.9.1/5.9.1/gcc_64/bin:$PATH
```
保持退出，然后```source /etc/profile```使生效


### 4 常见问题

#### 4.1 运行安装程序报错error while loading shared libraries...

报错信息如下
```
[root@VM-0-7-centos opt]# ./qt-opensource-linux-x64-5.9.1.run
./qt-opensource-linux-x64-5.9.1.run: error while loading shared libraries: libfontconfig.so.1: cannot open shared object file: No such file or directory
```

解决方法
```
yum -y install fontconfig-devel
```


#### 4.2 运行安装程序报错QXcbConnection: Could not connect to display 

缺少图形界面，需要先安装图形界面，云服务器图形界面安装参考：https://cloud.tencent.com/developer/article/1758509