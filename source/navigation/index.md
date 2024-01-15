---
title: 导航
banner_img: https://static.kevinchu.top/blog/assets/img/bg_018.jpg
---
<style>

.hitokoto-wrap {
    position: relative;
    top: -30px;
    width: 730px;
    max-width: 80%;
    border: 2px solid #797979;
    border-top: none;
    text-align: center;
    margin: 80px auto;
}

.hitokoto-wrap h1 {
    font-size: 20px;
    position: relative;
    margin-top: -20px;
    display: inline-block;
    letter-spacing: 4px;
    /* color: #797979 */
}

.hitokoto-wrap h1:hover {
    cursor:pointer;
    font-weight: bolder;
}

.hitokoto-wrap p {
    width: 70%;
    margin: auto;
    line-height: 30px;
    /* color: #797979; */
}

.hitokoto-wrap p#hitokoto {
    top: 5px;
    position: relative;
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

.widget-border {
    display: flex;  
    flex-wrap: wrap;
    justify-content: center;      
}

.widget-item {
    max-width: 180px;
}

</style>
<script src="https://static.kevinchu.top/blog/assets/js/sakura.js"></script>

<div class="hitokoto-wrap">
<div class="hitokoto-border hitokoto-left"></div>
<div class="hitokoto-border hitokoto-right"></div>
<h1 id="refresh">🔉每日一言</h1>
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


#### 🔨 工具

<br>

- [**🔗在线工具**](https://tool.lu/)

- [**🔗菜鸟工具**](https://c.runoob.com/)

- [**🔗UU在线工具**](https://uutool.cn/)

- [**🔗BeautifyTools**](https://beautifytools.com/)

- [**🔗draw.io**](https://www.draw.io/)

<br>

#### 💻 技术

<br>

- [**🔗C语言网**](https://www.dotcpp.com/course/)

- [**🔗C语言中文网**](http://c.biancheng.net/)

- [**🔗Hello算法**](https://www.hello-algo.com/)

- [**🔗JavaGuide**](https://javaguide.cn/)

- [**🔗Java全栈知识体系**](https://www.pdai.tech/)

- [**🔗美团技术团队博客**](https://tech.meituan.com/)

- [**🔗LeetCode**](https://leetcode.cn/)

- [**🔗牛客网**](https://www.nowcoder.com/)

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

<br>

#### 👾 搞怪 

<br>

- [**🔗宝可梦杂交大师**](https://pokemon.alexonsager.net/zh/)

- [**🔗神奇海螺试验场**](https://lab.magiconch.com/)

- [**🔗The Useless Web**](https://theuselessweb.com/)   

<br>



<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/sakana-widget@2.5.0/lib/sakana.min.css"
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
    
    new SakanaWidget({ character: 'pokemon-pikachu' }).mount('#pokemon-pikachu-widget');
    new SakanaWidget({ character: 'pokemon-psyduck' }).mount('#pokemon-psyduck-widget');
    new SakanaWidget({ character: 'pokemon-slowpoke' }).mount('#pokemon-slowpoke-widget');
    new SakanaWidget({ character: 'pokemon-bulbasaur' }).mount('#pokemon-bulbasaur-widget');

  }
</script>

<script
  async
  onload="initSakanaWidget()"
  src="https://cdn.jsdelivr.net/npm/sakana-widget@2.5.0/lib/sakana.min.js"
></script>

<br>

---

[***📌更新中，敬请期待！***]()



