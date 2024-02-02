---
title: 为你的网站添加节日灯笼挂件
index_img: https://static.kevinchu.top/blog/assets/img/cover_023.jpeg
archive: false
date: 2024-02-02 14:37:08
updated: 2024-02-02 14:37:08
tags:
    - css
categories:
    - 博客建站
---

## 前言
还有一周就要过年了，大家有没有从忙碌的学习和工作中放松下来，准备迎接新年的到来呢？🧨

这里介绍一个给网站添加节日灯笼挂件的方法，能够为你的网站增添些许节日的气息。

话不多说，直接上代码。


## 示例代码

```html
<style type="text/css">
    .lantern-box {
        position: fixed;
        top: -25px;
        right: 95px;
        z-index: 9999;
        pointer-events: none;
    }

    .lantern-box1 {
        position: fixed;
        top: -15px;
        right: -5px;
        z-index: 9998;
        pointer-events: none;
    }

    .lantern {
        position: relative;
        width: 120px;
        height: 90px;
        margin: 50px;
        background: #d8000f;
        background: rgba(216, 0, 15, .8);
        border-radius: 50% 50%;
        -webkit-transform-origin: 50% -100px;
        -webkit-animation: swing 5s infinite ease-in-out;
        box-shadow: -5px 5px 30px 4px #fc903d;
    }

    .lantern-a {
        width: 100px;
        height: 90px;
        background: #d8000f;
        background: rgba(216, 0, 15, .1);
        margin: 12px 8px 8px 8px;
        border-radius: 50% 50%;
        border: 2px solid #dc8f03;
    }

    .lantern-b {
        width: 45px;
        height: 90px;
        background: #d8000f;
        background: rgba(216, 0, 15, .1);
        margin: -4px 8px 8px 26px;
        border-radius: 50% 50%;
        border: 2px solid #dc8f03;
    }

    .lantern-line {
        position: absolute;
        top: -35px;
        left: 60px;
        width: 2px;
        height: 35px;
        background: #dc8f03;
    }

    .tassel-a {
        position: relative;
        width: 5px;
        height: 20px;
        margin: -5px 0 0 59px;
        -webkit-animation: swing 4s infinite ease-in-out;
        -webkit-transform-origin: 50% -45px;
        background: orange;
        border-radius: 0 0 5px 5px
    }

    .tassel-b {
        position: absolute;
        top: 14px;
        left: -2px;
        width: 10px;
        height: 10px;
        background: #dc8f03;
        border-radius: 50%;
    }

    .tassel-c {
        position: absolute;
        top: 18px;
        left: -2px;
        width: 10px;
        height: 35px;
        background: orange;
        border-radius: 0 0 0 5px;
    }

    .lantern:before {
        position: absolute;
        top: -7px;
        left: 29px;
        height: 12px;
        width: 60px;
        content: " ";
        display: block;
        z-index: 999;
        border-radius: 5px 5px 0 0;
        border: solid 1px #dc8f03;
        background: orange;
        background: linear-gradient(to right, #dc8f03, orange, #dc8f03, orange, #dc8f03);
    }

    .lantern:after {
        position: absolute;
        bottom: -7px;
        left: 10px;
        height: 12px;
        width: 60px;
        content: " ";
        display: block;
        margin-left: 20px;
        border-radius: 0 0 5px 5px;
        border: solid 1px #dc8f03;
        background: orange;
        background: linear-gradient(to right, #dc8f03, orange, #dc8f03, orange, #dc8f03);
    }

    .lantern-t {
        margin-top: 10px;
        font-family: KaiTi, Arial, Lucida Grande, Tahoma, sans-serif;
        font-size: 1.5rem;
        color: #f8b63b;
        font-weight: 700;
        line-height: 35px;
        text-align: center;
    }

    .lantern-box,
    .lantern-box1,
    .lantern-t {
        background: 0 0 !important;
    }

    @-moz-keyframes swing {
        0% {
            -moz-transform: rotate(-10deg)
        }

        50% {
            -moz-transform: rotate(10deg)
        }

        100% {
            -moz-transform: rotate(-10deg)
        }
    }

    @-webkit-keyframes swing {
        0% {
            -webkit-transform: rotate(-10deg)
        }

        50% {
            -webkit-transform: rotate(10deg)
        }

        100% {
            -webkit-transform: rotate(-10deg)
        }
    }

    @media screen and (max-width: 992px) {
        .lantern-widget {
            display: none;
        }
    }
</style>
<div class ="lantern-widget" >
    <div class="lantern-box">
        <div class="lantern">
            <div class="lantern-line">
            </div>
            <div class="lantern-a">
                <div class="lantern-b">
                    <div class="lantern-t">新年
                    </div>
                </div>
            </div>
            <div class="tassel-a">
                <div class="tassel-c">
                </div>
                <div class="tassel-b">
                </div>
            </div>
        </div>
    </div>
    <div class="lantern-box1">
        <div class="lantern">
            <div class="lantern-line">
            </div>
            <div class="lantern-a">
                <div class="lantern-b">
                    <div class="lantern-t">快乐 
                    </div>
                </div>
            </div>
            <div class="tassel-a">
                <div class="tassel-c">
                </div>
                <div class="tassel-b">
                </div>
            </div>
        </div>
    </div>
</div>
```

放到网站任意合适的位置即可，样式和文案可以根据需要进行调整🎈。


## 参考效果

如图，总体来说感觉还是不错的😎：
![](https://static.kevinchu.top/blog/public/lantern.jpg)