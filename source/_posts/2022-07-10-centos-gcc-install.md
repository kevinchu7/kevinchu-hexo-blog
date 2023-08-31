---
title: CentOS7安装gcc-5.4.0
index_img: https://static.kevinchu.top/blog/assets/img/cover_029.jpeg
date: 2022-07-10 23:07:34
updated: 2022-07-10 23:07:34
tags:
    - C/C++
    - Linux
    - 服务器
categories:
    - 软件工具
---
### 1 准备工作


#### 1.1 联网下载gcc5.4.0源码包
可用下载地址：https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.gz

#### 1.2 解压后运行下载脚本
```
tar xvf gcc-5.4.0.tar.gz
cd gcc-5.4.0
./contrib/download_prerequisites
```

#### 1.3 重新打包含依赖的文件夹(可选)
依赖包下载完成后，将包含依赖包的文件夹重新打包
```
tar -czvf gcc-5.4.0-withdepends.tar.gz gcc-5.4.0/*
```

### 2 安装


#### 2.1 复制保护依赖的完整包到目标机器解压(可选)
```
tar -xzvf gcc-5.4.0-withdepends.tar.gz
cd gcc-5.4.0-withdepends
```

#### 2.2 配置编译选项，生产Makefile文件
```
./configure --prefix=/usr/local --enable-checking=release --enable-languages=c,c++ --disable-multilib

```
>编译参数说明：
>--prefix=/usr/local/ 指定安装路径，prefix很重要一定要填
--enable-checking=release 以软件发布版的标准来对编译时生成的代码进行一致性检查；设置该选项为 enable并不会改变编译器生成的二进制结果，但是会导致编译的时间增加；该选项仅支持gcc编译器；
--enable-languages=c,c++ 支持的高级语言类型和运行时库，可以设置的所有语言包括 ada,c,c++,Fortran,java,objc,obj-c++,GO 等语言。
--disable-multilib 如果你的操作系统是32位，默认就已经设置为 disable，这意味着gcc仅能生成32位的可执行程序；如果你的操作系统是64位，默认就已经设置为 enable。如果在64位系统上，要禁止生成32位代码， 设置 --disable-multilib。


#### 2.3 编译安装

若无```g++```环境，先安装```g++```
```
sudo yum install gcc-c++
```
开始编译和安装，过程会比较久（要个把小时，看机器配置）
```
make
make install
```

#### 2.4 重建软链接

安装后执行```gcc -v``` 发现版本不是5.4.0，需要对引用做处理

```
cd /usr/bin/
mv gcc gcc_back
mv g++ g++_back
ln -s /usr/local/bin/gcc gcc
ln -s /usr/local/bin/g++ g++
```

此时再次使用```gcc -v```可查看到显示的版本号已是5.4.0


### 3 常见问题


#### 3.1 安装gcc出现错误：configure: error: error verifying int64_t uses long long
缺少```g++```环境，安装g++
```
sudo yum install gcc-c++
```


#### 3.2 运行程序报错：/lib64/libstdc++.so.6: version `CXXABI_1.3.8' not found

解决方法参考：
https://blog.csdn.net/u012322399/article/details/120065301

---

参考链接：

- https://blog.csdn.net/hezhanran/article/details/121563713
- https://www.cnblogs.com/w84422/p/15210919.html 
- https://blog.csdn.net/tuibianhuaisheng/article/details/115399019