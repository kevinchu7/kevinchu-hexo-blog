---
title: VScode配置C/C++环境(windows)
index_img: https://static.kevinchu.top/blog/assets/img/cover_027.jpeg
date: 2022-06-23 00:14:25
updated: 2022-06-23 00:14:25
tags:
    - VScode
    - C
    - C++
categories:
    - 软件工具
---
>本文已于2023-07-06 14:25:25更新

## 1  MinGW编译器下载和配置

### 1.1 下载MinGW编译器

[下载地址](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)（下载太慢需要科学上网）
- 在线安装——下载MinGW-W64-install.exe（速度较慢）
- 下载压缩包——×86_64-posix-sjlj（速度较快）

![](https://static.kevinchu.top/blog/public/20220624002658.png)

下载完成，解压后：
![](https://static.kevinchu.top/blog/public/20220624003402.png)


### 1.2 配置环境变量

Path里新建一条指向MinGW解压后的bin目录

![](https://static.kevinchu.top/blog/public/20220624003933.png)

校验是否配置成功：
cmd里输入```gcc -v```回车，结果如下：

![](https://static.kevinchu.top/blog/public/20220624004119.png)

则配置成功。


## 2 VScode配置C/C++环境

### 2.1 VScode安装插件

必要插件-C/C++:
![](https://static.kevinchu.top/blog/public/20220624004655.png)

推荐-C/C++ Extension Pack:
(包含了 vscode 编写 C/C++ 工程需要的插件C/C++、C/C++ Themes、CMake、CMake Tools等)
![](https://static.kevinchu.top/blog/public/20230706112943.png)

安装完后重启VScode。


### 2.2 配置环境参数

- 1.创建C/C++项目文件夹

- 2.在项目文件夹下新建```.vscode```配置文件目录，在里面添加三个配置文件c_cpp_properties.json，launch.json，tasks.json，亦可通过以下方式快速创建：


***launch.json***: Run->Add Configuration...->任选一模板
![](https://static.kevinchu.top/blog/public/20230706114138.png)


***tasks.json***: Terminal->Configure Tasks...->Create tasks.json file from template->任选一模板
![](https://static.kevinchu.top/blog/public/20230706140153.png)![](https://static.kevinchu.top/blog/public/20230706140335.png)



***c_cpp_properties.json***: 点开项目文件下的任一文件，在vscode右下角找到*Win32*按钮并点击->Edit Configuration (JSON)
![](https://static.kevinchu.top/blog/public/20230706140927.png)![](https://static.kevinchu.top/blog/public/20230706141352.png)



---
三个文件内容配置参考（清空默认内容，复制粘贴，修改mingw64路径）：




c_cpp_properties.json
```JSON
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "windowsSdkVersion": "10.0.17763.0",
            "compilerPath": "D:\\Environment\\mingw64\\bin\\g++.exe", /*修改成自己bin目录下的g++.exe，这里的路径和电脑里复制的文件目录有一点不一样，这里是两个反斜杠\\*/
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "${default}"
        }
    ],
    "version": 4
}

```


launch.json
```JSON
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\Environment\\mingw64\\bin\\gdb.exe", /*修改成自己bin目录下的gdb.exe，这里的路径和电脑里复制的文件目录有一点不一样，这里是两个反斜杠\\*/
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "task g++"
        }
    ]
}
```


tasks.json
```JSON
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558 
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "task g++",
            "command": "D:\\Environment\\mingw64\\bin\\g++.exe", /*修改成自己bin目录下的g++.exe，这里的路径和电脑里复制的文件目录有一点不一样，这里是两个反斜杠\\*/
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe",
                "-I",
                "D:\\Workspace\\cpp-projects", /*修改成自己放c/c++项目的文件夹，这里的路径和电脑里复制的文件目录有一点不一样，这里是两个反斜杠\\*/
                "-std=c++17"
            ],
            "options": {
                "cwd": "D:\\Environment\\mingw64\\bin" /*修改成自己bin目录，这里的路径和电脑里复制的文件目录有一点不一样，这里是两个反斜杠\\*/
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": "build",
        }
    ]
}

```




## 3 测试运行


在搭好环境的目录下，创建hello-world项目，输入以下测试代码：
```C
#include <stdio.h>
#include <windows.h>
int main()
{
    printf("Hello World\n");
    system("pause");
    return 0;
}
```
目录结构：

![](https://static.kevinchu.top/blog/public/20220624010535.png)

按F5运行代码hello_world.cpp:

![](https://static.kevinchu.top/blog/public/20220624010650.png)

成功！

## 4 常见问题

- c语言程序窗口执行到scanf函数出现闪退
可在头文件加上#include<stdlib>，main函数里return上面加上system("pause");  可防止程序窗口闪退
- c++直接加system("pause")，防止窗口闪退


---
>本文参考自：https://blog.csdn.net/weixin_48468423/article/details/118950592