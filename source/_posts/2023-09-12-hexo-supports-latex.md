---
title: Hexo支持LaTeX数学公式渲染
index_img: https://static.kevinchu.top/blog/assets/img/cover_010.jpeg
archive: false
date: 2023-09-12 19:16:59
updated: 2023-09-14 19:16:59
tags:
    - Hexo
    - LaTeX
    - MathJax
    - KaTeX
categories:
    - 博客建站
---

## 1 前言
当前主流的LaTeX公式渲染引擎主要是[MathJax](https://github.com/mathjax/MathJax-src)和[KaTex](https://github.com/KaTeX/KaTeX)，MathJax几乎支持所有的LaTeX，功能强大，但是渲染速度相对较慢；KaTex更为轻量，渲染速度快，但是对部分复杂的公式不支持。两者各有利弊，都很实用。

Hexo博客默认的markdown渲染器[hexo-renderer-marked](https://github.com/hexojs/hexo-renderer-marked)不支持渲染LaTeX公式，也不能扩展插件，想要在博客中渲染LaTeX往往需要更换markdown渲染器以使用公式引擎对公式进行渲染。常见可用的markdown渲染器有[hexo-renderer-pandoc](https://github.com/wzpan/hexo-renderer-pandoc)、[hexo-renderer-markdown-it](https://github.com/hexojs/hexo-renderer-markdown-it)、[hexo-renderer-markdown-it-plus](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus)等等，通常要配合插件一起使用，且可能需要应对各类样式问题和兼容性问题。

Hexo官方还对Hexo 5.0以上版本推出了[hexo-math](https://github.com/hexojs/hexo-math)插件来帮助识别两个公式引擎的语法，实现对LaTeX的渲染。该方式支持更多定制化参数，很方便，不过通常需要进行配置，且使用时要用较为繁琐的符号将需要渲染的公式内容包裹起来。

本文介绍一种最原始、最直接的方式，不用更换markdown渲染器，也无需安装和配置插件，直接在需要渲染公式的文章中引入LaTeX自动渲染js来实现对LaTaX公式的渲染。

## 2 引入LaTeX自动渲染脚本实现LaTaX公式渲染

### 2.1 使用方式
选择你想要使用的引擎（MathJax或KaTeX选其一），直接在markdown文章内容中添加引入对应引擎自动渲染js的代码即可（任意位置，建议放在内容首部或尾部），引入代码参考2.2和2.3。

>如果觉得这种方法不够优雅，也可以使用Hexo 5.0的注入器或者各个主题自带的注入器将代码注入到需要的页面。另一种思路是，建一个带引入代码的文章模板，需要写带有LaTeX公式的博客时，直接通过该模板创建文章。

**注意**：
Hexo在markdown文章中写LaTeX公式时，有个经典问题就是一些字符会被markdown渲染器误认为是转义符号而被丢失，之后公式引擎再对公式渲染时语义已经改变导致渲染出错，最典型的例子就是换行符`\\`会被转义成`\`，导致最终渲染的公式内容不换行。本方式会存在这样的问题，我的应对方法是，针对有可能被转义的公式（如带换行符的），用`<span>`或者`<div>`标签将LaTeX公式包裹起来，这样公式内容就不会被markdown渲染器识别为转义字符。注意`<span>`或`<div>`标签应该与markdown内容分隔开，最好空出一行然后写标签，标签独占一行，以免被markdown渲染干扰。

示例，某矩阵LaTeX公式：
```LaTeX
$$
\begin{pmatrix}  
  a_{11} & a_{12} & a_{13} \\  
  a_{21} & a_{22} & a_{23} \\  
  a_{31} & a_{32} & a_{33}  
\end{pmatrix} 
$$
```
直接写，渲染异常：

$$
\begin{pmatrix}  
  a_{11} & a_{12} & a_{13} \\  
  a_{21} & a_{22} & a_{23} \\  
  a_{31} & a_{32} & a_{33}  
\end{pmatrix} 
$$

使用`<span>`标签包裹：
```LaTeX
<span>
$$
\begin{pmatrix}  
  a_{11} & a_{12} & a_{13} \\  
  a_{21} & a_{22} & a_{23} \\  
  a_{31} & a_{32} & a_{33}  
\end{pmatrix}
$$
</span>
```

效果如下，渲染正常：

<span>
$$
\begin{pmatrix}  
  a_{11} & a_{12} & a_{13} \\  
  a_{21} & a_{22} & a_{23} \\  
  a_{31} & a_{32} & a_{33}  
\end{pmatrix}
$$
</span>

>当然也可以采用其他的处理方式，参考这篇[博客](https://shomy.top/2016/10/22/hexo-markdown-mathjax)。

### 2.2 引入MathJax自动渲染js

引入MathJax自动渲染脚本代码（需开启对`$`分隔符的支持）：
```html
<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>
```

更多配置参考：[MathJax的官方文档](https://docs.mathjax.org/en/latest/web/start.html)

### 2.3 引入KaTeX自动渲染js

引入KaTeX自动渲染脚本代码（和2.2MathJax二者选其一即可）：
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.css" integrity="sha384-GvrOXuhMATgEsSwCs4smul74iXGOixntILdUW9XmUC6+HX0sLNAK3q71HotJqlAn" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.js" integrity="sha384-cpW21h6RZv/phavutF+AuVYrr+dA8xD9zs6FwLpaCct6O9ctzYFfFr4dgmgccOTx" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/contrib/auto-render.min.js" integrity="sha384-+VBxd3r6XgURycqtZ117nYw44OOcIax56Z4dCRWbxyPt0Koah1uHoK0o4+/RRE05" crossorigin="anonymous"></script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\\(', right: '\\)', display: false},
              {left: '\\[', right: '\\]', display: true}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
```

更多配置参考：[KaTeX的官方文档](https://katex.org/docs/autorender)


## 3 LaTeX数学公式写法

### 3.1.插入方式

| **类型** | **写法** |
| :--------: | :-------: |
| 行中公式 | `$...$` |
| 独立公式 | `$$...$$` |

示例：

这是行中公式（默认左对齐）：$\frac{n!}{k!(n-k)!} = \binom{n}{k}$

这是独立公式（默认居中对齐）：$$\frac{n!}{k!(n-k)!} = \binom{n}{k}$$


### 3.2 语法

LaTeX语法参考：
- [**LaTeX公式手册**](https://blog.csdn.net/bwqiang/article/details/106029183)
- [**LaTeX公式编辑器帮助文档**](https://www.latexlive.com/help)
- [**KaTeX官网教程**](https://katex.org/docs/supported.html)
- [**Wikibooks教程**](https://en.wikibooks.org/wiki/LaTeX/Mathematics)


好用的LaTeX在线工具：
- [**LaTeX公式编辑器**](https://www.latexlive.com)


<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>

