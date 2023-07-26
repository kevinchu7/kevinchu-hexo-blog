---
title: Phabricator Arcanist代码审核配置
index_img: https://static.kevinchu.top/blog/assets/img/cover_012.jpeg
date: 2022-02-28 00:06:07
updated: 2022-02-28 00:06:07
tags:
    - Phabricator
categories:
    - 软件工具
---
# 1 Phabricator简介
Phabricator是一套基于Web的软件开发协作工具，一个Web应用用于帮助软件公司构建更好的软件。 
原是facebook员工开发的可视化代码评审工具，现在离开facebook去Phacility，并持续维护。 
>Phabricator是一套基于Web的软件开发协作工具，它的主要功能有操作数据（任务查看操作）;开发辅助（工作流，查看提交的 diff，代码检查，执行单元测试）;辅助（文件文档管理）。它最初是Facebook的一个内部工具，后来发展开源。
官网：https://www.phacility.com/
项目地址：https://github.com/phacility/phabricator
官方界面参考：[Arcanist User Guide: Windows](https://secure.phabricator.com/)

Phabricator是一个LAMP (Linux, Apache, MySQL, PHP) 程序，所以Windows下是不能安装的。平台部署在服务器上，提供账号注册给新用户，而开发成员在本地需要安装配置的是它的代码审查辅助工具Arcanist。
工作原理图：
![](https://static.kevinchu.top/blog/public/phabricator-arcanist.png)


# 2 windows安装配置Arcanist

## 2.1 环境准备 

### 2.1.1 安装配置svn/git环境

### 2.1.2 安装PHP
- [PHP官网](https://windows.php.net/download#php-7.3)下载安装,将文件解压到一个指定的目录（如E:\PHP）；
- 将PHP安装目录（如E:\PHP）加入到PATH环境变量中；
- 复制php.ini-development文件并重命名为php.ini，并且把“extension_dir= "ext"”、“extension=php_curl.dll”、“extension=php_mbstring.dll”这几行行首的分号去掉（没有的话可以自行添加这几项）；
- 测试，控制台输入php -i， 如果报错无法启动此程序，因为计算机中丢失 VCRUNTIME140.dll，考虑缺少Visual C++Redistributable 2015，前去[微软官网](https://www.microsoft.com/en-us/download/confirmation.aspx?id=48145)下载安装即可；

### 2.1.3 安装arcanist和libphutil
- 下载[arcanist](https://github.com/facebook/arcanist/archive/master.zip)和[libphutil](https://github.com/facebook/libphutil/archive/master.zip)（libphutil是php的工具集，Arcanist运行需要依赖它）；
- 下载后解压到指定的Phabricator目录（如D:\phabricator），并把目录名分别修改为arcanist、libphutil，并把arc.bat所在目录（例如D:\phabricator\arcanist\bin）配置到PATH环境变量中；
- 测试，控制台输入arc，显示```USAGE EXCEPTION  Choose a workflow!```；


## 2.2 配置
### 2.2.1 配置arc编辑器
使用Arcanist时需要使用文本编辑器编辑大块的文本，不能使用微软自带的NotePad，有以下Windows平台的编辑器可供选择：
Notepad++
```CMD
$ arc set-config editor
““C:\Program Files (x86)\Notepad++\notepad++.exe” -multiInst -nosession”
```
vim
```CMD
$ arc set-config editor ““C:\Program Files (x86)\Git\share\vim\vim73\vim.exe””
```
GitPad
```CMD
$ arc set-config editor ““C:\Users\yourusername\AppData\Roaming\GitPad\GitPad.exe””
```
Sublime Text
```CMD
$ arc set-config editor
““C:\Program Files\Sublime Text 2\sublime_text.exe” -w -n”
```
(以上编辑器的具体路径可改为自己的编辑器安装地址。)

### 2.2.2 arc项目初始化设置
- 在项目工程根目录下添加文件.arcconfig，输入：
```JSON
{
   "phabricator.uri" : "https://pms.hscf.com" (注意：第二个引号里的内容，是你的phabricator的URL)
}
```
- 安装证书：项目工程根目录下运行命令arc install-certificate，依照提示访问http://$pha-server/conduit/token/， 复制内容粘贴token即可（在你执行这步之前，你必须在Phabricator有一个已存在的账号），至此配置成功。


# 3 Arcanist的使用
核心命令：
 ```arc diff [last_commit]```
>- 此命令会将last_commit到最新的commit之间的改动的所有代码发送到phabricator平台，并创建一个revision；
>- 如果没有指定last_commit，则会使用master分支来作为last_commit的起点；
>- 运行命令后会弹出一个编辑面板，在此面板中你需要指定reviewers，即指定一个或多个给你review code的人；
>- 如果指定了多个reviewers，则其中任何一个review通过就可以，不用全部review通过；
>- 另外编辑面板中也有一个test plan选项，国外人一般每个项目都有测试，所以此项是必填的，如果没有测试，我们只需要随便设置一个字段即可，比如设置成```no``` 或```skip```；
>- 如果觉得指派reviewer的用法不太方便,可以执行```arc diff --preview```根据生成的diff页面进入配置，生成revision；
>- 以上操作之后，系统会发送邮件通知reviewers去review你的代码，你也可以通过```arc list```来查看当前review的状态；
>- 如果review没有通过，你需要在原来的基础上修改，修改完并commit之后需要执行```arc diff [last_commit] --update D(id)```继续review；
>- 如果review通过了，只需要运行```arc land --onto some_branch```，arc会将你当前分支合并到master上，并删除当前分支。
