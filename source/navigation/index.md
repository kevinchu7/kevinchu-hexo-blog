---
title: 导航
banner_img: https://static.kevinchu.top/blog/assets/img/bg_018.jpg
comment: ''
---
<style>
.hitokoto-wrap {
    position: relative;
    width: 730px;
    max-width: 80%;
    border: 2px solid #797979;
    border-top: none;
    text-align: center;
    margin: 80px auto;
}

.hitokoto-wrap h1 {
    font-size: 25px;
    position: relative;
    margin-top: -20px;
    display: inline-block;
    letter-spacing: 4px;
    color: #797979
}

.hitokoto-wrap p {
    width: 70%;
    margin: auto;
    line-height: 30px;
    color: #797979;
}

.hitokoto-wrap p#hitokoto {
    font-size: 25px;
}

.hitokoto-wrap p#info {
    font-size: 15px;
    margin: 15px auto;
    text-align: right;
}

.hitokoto-border {
    position: absolute;
    height: 2px;
    width: 27%;
    background-color: #797979;
}

.hitokoto-right {
    right: 0;
}

.hitokoto-left {
    left: 0;
}

@media (max-width: 685px) {
    .hitokoto-border {
        width: 18%;
    }
}

@media (max-width: 500px) {
    .hitokoto-wrap {
        margin-top: 60px;
        margin-bottom: 20px;
        border-top: 2px solid #797979;
    }

    .hitokoto-wrap h1 {
        margin: 20px 6px;
    }

    .hitokoto-border {
        display: none;
    }
}
</style>
<script src="https://cdn.jsdelivr.net/gh/wallleap/cdn/js/sakura.js"></script>

<div class="hitokoto-wrap">
<div class="hitokoto-border hitokoto-left"></div>
<div class="hitokoto-border hitokoto-right"></div>
<h1>每日一言</h1>&nbsp;<h1 id="refresh">⏭</h1>
<p id="hitokoto"></p>
<p id="info"></p>
<script>
    function fetchHitokoto() {
        const text = document.querySelector('#hitokoto');
        const info = document.querySelector('#info');
        text.innerText = '挑选中...';
        info.innerText = '';
        fetch('https://v1.hitokoto.cn/?c=a&c=b&c=c&c=d&c=h&c=k', {
            cache: "no-store"
            }
        ).then(response => response.json())
        .then(data => {
            text.innerText = data.hitokoto;
            info.innerText = '出自：' + data.from;
                console.log(data)
        })
        .catch(console.error);
    }
    var refreshBtn = document.getElementById("refresh");
    refreshBtn.onclick = function () {
        fetchHitokoto();
    }
    fetchHitokoto();
</script>
</div>

---

#### 🔨 工具

<br>

- [**🔗在线工具**](https://tool.lu/)

- [**🔗UU在线工具**](https://uutool.cn/)

<br>

#### 💻 学习

<br>

- [**🔗C语言网**](https://www.dotcpp.com/course/)

- [**🔗Hello算法**](https://www.hello-algo.com/)

- [**🔗JavaGuide**](https://javaguide.cn/)

- [**🔗Java全栈知识体系**](https://www.pdai.tech/)

- [**🔗美团技术团队博客**](https://tech.meituan.com/)

<br>

#### 📱 娱乐

<br>

- [**🔗今日热榜**](https://tophub.today/)

<br>

#### 🔍 综合

<br>

- [**🔗AK47导航**](https://www.ak47s.cn/)

- [**🔗炫猿导航**](https://xydh.fun/)

<br>

---

[***🛸更多内容，敬请期待👀***](https://www.baidu.com/)


