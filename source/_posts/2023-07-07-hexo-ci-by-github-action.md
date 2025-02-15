---
title: Hexo结合GitHub Action实现博客自动部署更新
index_img: https://static.kevinchu.top/blog/assets/img/cover_046.jpeg
archive: false
date: 2023-07-07 01:15:26
updated: 2025-02-10 18:15:26
tags:
    - Hexo
    - CI/CD
    - Github
categories:
    - 博客建站
---
>***我回来啦！***
>
>最近这几个月因为忙于工作和各种事情，一直没抽出时间更新博客，今天一个契机要修改一篇老文章，本人终于打起了精神(~~从椅子上跳起来~~)，打算写篇新博客。
>
>这几个月的时间里我也积累了一些知识和经验，打算接下来抽时间记录下来，而今天还是用来了结一个想了许久没去做的事儿——回忆一下本博客的搭建流程。
>
>距本站的搭建已经过去非常久的时间了，个中细节可能已经记不太清，特此回忆一下大致的流程，记录下来，以防遗忘。
---



### 1.关于博客框架和主题

#### 1.1 Hexo框架

如你所见，本博客使用静态博客框架Hexo进行搭建和渲染，关于Hexo框架的详细介绍见官网: [**Hexo**](https://hexo.io/) 。

#### 1.2 Fluid主题

本博客使用了[**Fluid主题**](https://github.com/fluid-dev/hexo-theme-fluid)，自定义主题配置，结合少部分魔改，渲染形成了现在的页面样式风格。


### 2.利用GitHub Action实现博客自动部署

#### 2.1 自动部署流程

![](https://static.kevinchu.top/blog/public/20230706181459.png)


优点：依靠GitHub仓库进行存储，自动部署的配置与项目代码一样得到备份，切换本地环境时无需再次对Hexo进行繁琐的环境配置，只需拉下代码更新提交即可完成博客更新，甚至可以直接在GitHub上更新博客，我认为这点是有效地弥补了Hexo这类静态博客配置繁琐、更新麻烦的劣势。

缺点：GitHub国内访问不稳定，代码提交经常失败，轻度依赖科学上网。

#### 2.2 GitHub Action - workflow文件示例

在```.github```目录下新建```workflows```文件夹，并在内添加workflow配置文件main.yml，内容参考：

```yml
name: Hexo-Blog-CI

on: 
  push:
    branches:
      - master
      
jobs:
  build: 
    runs-on: ubuntu-latest 

    steps:
    - name: 检出仓库代码
      uses: actions/checkout@master

    - name: 安装Node.js 14.x 
      uses: actions/setup-node@master
      with:
        node-version: "14.x"

    - name: 安装Hexo依赖
      run: |
        npm install hexo-cli -g
        npm install

    - name: 构建Hexo
      run: |
        hexo clean
        hexo generate
    
    - name: 安装腾讯云依赖
      run: |
        sudo pip install coscmd
        sudo pip install tccli

    - name: 配置腾讯云依赖
      env:
        SECRET_ID: ${{ secrets.TCLOUD_API_ID }} # 腾讯云API ID(使用secrets，避免明文)
        SECRET_KEY: ${{ secrets.TCLOUD_API_KEY }} # 腾讯云API ID(同上)
        BUCKET: ${{ secrets.TCLOUD_COS_BUCKET_NAME }} # 腾讯云COS存储桶名称(同上)
        REGION: ${{ secrets.TCLOUD_COS_REGION }} # 腾讯云COS存储桶地区(同上)
      run: |
        coscmd config -a $SECRET_ID -s $SECRET_KEY -b $BUCKET -r $REGION
        tccli configure set secretId $SECRET_ID
        tccli configure set secretKey $SECRET_KEY
        tccli configure set region $REGION

    - name: 上传到腾讯云COS并刷新CDN
      run: |
        coscmd upload -rfs --delete ./public/ /
        tccli cdn PurgePathCache --cli-unfold-argument --Paths https://博客CDN地址/ --FlushType flush
        tccli cdn PurgePathCache --cli-unfold-argument --Paths https://静态资源CDN地址/ --FlushType flush

```

**2025年更新：**
由于github action默认的python版本升高，3.12版本python已不在支持```SafeConfigParser```这个类，使用coscmd工具时会报错```cannot import name 'SafeConfigParser' from 'configparser'```。

参考官方仓库issues里给出建议，对配置文件做以下修改：
- 修改前：```sudo pip install coscmd```
- 修改后：```sudo pip install https://github.com/tencentyun/coscmd/archive/refs/heads/master.zip```


### 3.其他

#### 3.1 关于资源存储与托管平台的选择

本站的网页及静态资源托管在[腾讯云COS对象存储](https://cloud.tencent.com/document/product/436)(同阿里云OSS)上，利用CDN服务进行加速，个人认为这是一套成本比较低且访问相对稳定的方案。当然我也折腾过其他各种各样的方案，感觉各有利弊。

>可选择的方案：
>代码托管：github、gitee、coding等
>
>CI：github action、gitee go、jenkins等
>
>资源托管：github/gitee pages、各类静态网页托管服务、oss/cos网页托管、云服务器等


#### 3.2 vscode插件推荐

vscode是一个强大的编辑器，有许多好用的插件，我觉得用来写Hexo博客非常合适。这里只介绍一个我认为对编写Markdown内容最实用的插件——PicGo。

![](https://static.kevinchu.top/blog/public/20230707135547.png)


在之前的博客中也介绍过PicGo这个软件，用它来上传图片到图床并获取对应的访问链接非常方便，参考这篇[文章](https://blog.kevinchu.top/2021/02/15/tuchuang/)。


而vscode中的PicGo插件，直接将原来软件中的核心功能完美移植了过来，只需简单配置一下的图床信息，就能在编辑Markdown时，截完图片按```Ctrl + Alt + U```(苹果用户```Cmd + Opt + U```)，实现将剪切板中的图片直接上传至指定图床并自动将访问链接粘贴到Markdown中，十分方便。

![](https://static.kevinchu.top/blog/public/20230707144350.png)




