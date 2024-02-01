---
title: 导航
banner_img: https://static.kevinchu.top/blog/assets/img/bg_018.jpg
---
<style>
.custom-module {
    width: 100%;
    padding: 0;
    text-align: center;
}

.module-title{
    font-size: 20px;
    position: relative;
    display: inline-block;
    letter-spacing: 4px;
    margin: 20px;
}

.hitokoto-wrap {
    position: relative;
    width: 730px;
    max-width: 80%;
    border: 2px solid #797979;
    text-align: center;
    margin: 0px auto;
}

.module-title span:hover {
    cursor:pointer;
    font-weight: bolder;
    border-bottom : 1px solid #797979;
}

.hitokoto-wrap p {
    width: 75%;
    margin: auto;
    line-height: 40px;
}

.hitokoto-wrap p#hitokoto {
    margin-top: 20px;
    margin-bottom: 10px;
    position: relative;
    font-size: 25px;
    text-align: center;
    /* text-indent: 2em;  */
}

.hitokoto-wrap p#info {
    font-size: 15px;
    margin: 15px auto;
    margin-top: 10px;
    margin-bottom: 20px;
    text-align: right;
}

.news {
    position: relative;
    width: 730px;
    max-width: 80%;
    text-align: center;
    margin: 0 auto;
    border: 2px solid #797979;
}

@media (max-width: 685px) {

}

@media (max-width: 500px) {
    .hitokoto-wrap {
        max-width: 88%;
    }

    .news {
        max-width: 88%;
    }

}

.widget-border {
    display: flex;  
    flex-wrap: wrap;
    justify-content: center;      
}

.widget-item {
    max-width: 180px;
}

</style>
<div class="custom-module">
<h1  class="module-title">📖每日一言<span id="refresh" style="font-size:10px">（刷新）</span></h1>
<div class="hitokoto-wrap">
<p id="hitokoto"></p>
<p id="info"></p>
<script>
    function fetchHitokoto() {
        const text = document.querySelector('#hitokoto');
        const info = document.querySelector('#info');
        text.innerText = '挑选中...';
        info.innerText = '';
        fetch('https://v1.hitokoto.cn/?c=a&c=b&c=c&c=d&c=f&c=h&c=i&c=k', {
            cache: "no-store"
            }
        ).then(response => response.json())
        .then(data => {
            text.innerText = data.hitokoto;
            info.innerText = '出自：' + data.from;
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


<h1 class="module-title">📺60s读懂世界</h1>

<div class="news">
<img src="https://api.jun.la/60s.php?format=image">
</div>



--- 


<h1 class="module-title">🌏网站导航</h1>


</div>

</br>

#### 🔨 工具

<br>

- [**🔗在线工具**](https://tool.lu/)

- [**🔗菜鸟工具**](https://c.runoob.com/)

- [**🔗UU在线工具**](https://uutool.cn/)

- [**🔗BeautifyTools**](https://beautifytools.com/)

- [**🔗CodePen**](https://codepen.io/)

- [**🔗draw.io**](https://www.draw.io/)

- [**🔗炸了么**](https://zhale.me/)

- [**🔗SpeedTest**](https://www.speedtest.cn/)

<br>

#### 💻 技术

<br>

- [**🔗HelloGitHub**](https://hellogithub.com/)

- [**🔗C语言网**](https://www.dotcpp.com/course/)

- [**🔗C语言中文网**](http://c.biancheng.net/)

- [**🔗JavaGuide**](https://javaguide.cn/)

- [**🔗Java全栈知识体系**](https://www.pdai.tech/)

- [**🔗Hello算法**](https://www.hello-algo.com/)

- [**🔗LeetCode**](https://leetcode.cn/)

- [**🔗牛客网**](https://www.nowcoder.com/)

- [**🔗美团技术团队博客**](https://tech.meituan.com/)

<br>

#### 📱 新闻

<br>

- [**🔗今日热榜**](https://tophub.today/)

- [**🔗慧语简报**](https://news.topurl.cn/)

<br>

#### 🔍 导航

<br>

- [**🔗AK47导航**](https://www.ak47s.cn/)

- [**🔗炫猿导航**](https://xydh.fun/)

- [**🔗沙雕导航网**](https://shadiao.pro/)

- [**🔗摸鱼导航**](https://moyu.games/)

<br>

#### 👾 搞怪 

<br>

- [**🔗宝可梦杂交大师**](https://pokemon.alexonsager.net/zh/)

- [**🔗神奇海螺试验场**](https://lab.magiconch.com/)

- [**🔗The Useless Web**](https://theuselessweb.com/)   

<br>


<link
  rel="stylesheet"
  href="https://static.kevinchu.top/blog/assets/css/sakana-widget-2.7.0.css"
/>

<div class="widget-border">
<div id="pokemon-pikachu-widget" class="widget-item"></div>
<div id="pokemon-psyduck-widget" class="widget-item"></div>
<div id="pokemon-slowpoke-widget" class="widget-item"></div>
<div id="pokemon-bulbasaur-widget" class="widget-item"></div>
</div>

<script>
  function initSakanaWidget() {
    const map = new Map([
        ['pokemon-pikachu','https://static.kevinchu.top/blog/assets/img/pokemon-pikachu.png'],
        ['pokemon-psyduck','https://static.kevinchu.top/blog/assets/img/pokemon-psyduck.png'],
        ['pokemon-slowpoke','https://static.kevinchu.top/blog/assets/img/pokemon-slowpoke.png'],
        ['pokemon-bulbasaur','https://static.kevinchu.top/blog/assets/img/pokemon-bulbasaur.png'],
        ['pokemon-charmander','https://static.kevinchu.top/blog/assets/img/pokemon-charmander.png'],
        ['pokemon-squirtle','https://static.kevinchu.top/blog/assets/img/pokemon-squirtle.png'],
        ['pokemon-eevee','https://static.kevinchu.top/blog/assets/img/pokemon-eevee.png'],
        ['pokemon-jigglypuff','https://static.kevinchu.top/blog/assets/img/pokemon-jigglypuff.png'],
        ['pokemon-cubone','https://static.kevinchu.top/blog/assets/img/pokemon-cubone.png'],
        ['pokemon-snorlax','https://static.kevinchu.top/blog/assets/img/pokemon-snorlax.png']
    ]);

    function registerSakana(name, img){
        const widget = SakanaWidget.getCharacter('chisato');
        widget.image = img;
        SakanaWidget.registerCharacter(name, widget);
    }

    map.forEach((val,key) => {
        registerSakana(key,val);
    })
    
    new SakanaWidget({ character: 'pokemon-charmander', controls: false, rod: false }).mount('#pokemon-pikachu-widget');
    new SakanaWidget({ character: 'pokemon-bulbasaur', controls: false, rod: false }).mount('#pokemon-psyduck-widget');
    new SakanaWidget({ character: 'pokemon-squirtle', controls: false, rod: false }).mount('#pokemon-slowpoke-widget');
    new SakanaWidget({ character: 'pokemon-jigglypuff', controls: false, rod: false }).mount('#pokemon-bulbasaur-widget');

  }
</script>

<script
  async
  onload="initSakanaWidget()"
  src="https://static.kevinchu.top/blog/assets/js/sakana-widget-2.7.0.js"
></script>


---


[***📌更新中，敬请期待！***]()



