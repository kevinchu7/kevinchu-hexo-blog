---
title: 关于
layout: "about"
---
## 🤖关于我
后端入门级玩家👨‍💻 | Pokemon精英训练师⚡️

<img src="https://static.kevinchu.top/blog/assets/img/pokemon-psyduck.gif" alt="psyduck">

## 🖥️关于本站
建立本站以记录学习、积累经验、归纳知识、~~分享生活~~、~~捣鼓折腾~~......
<br>


> **📝大事记**
> - 2023-07-30　　🍊加入十年之约

<br>

> **🧾搬迁历史**
> - 2022-04-05　　🚅转折
暂停Halo博客，重新利用Hexo建站，采用Fluid主题，托管于腾讯云，作为博客主站。
><br>
><br>
>- 2022-01-03　　🚕里程
基于腾讯云开发部署Halo博客，作为第二站点。
><br>
><br>
>- 2021-05-15　　🦽转折
放弃自建博客，转战博客园。
><br>
><br>
>- 2021-03-04　　🚗里程
放弃WordPress，入坑Typecho，采用付费主题handsome。
><br>
><br>
>- 2020-12-14　　🏍️里程
放弃静态博客，入坑WordPress，采用Sakura主题，部署在购买的服务器上。
><br>
><br>
>- 2020-10-18　　🛵里程
增加部署节点至coding静态页面托管服务，作为国内镜像。
><br>
><br>
>- 2020-08-17　　🚲起点
基于Hexo框架搭建了第一个博客，采用Butterfly主题，托管于GitHub Pages。


<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/sakana-widget@2.5.0/lib/sakana.min.css"
/>

<div id="pokemon-pikachu-widget" style="float:left"></div>
<div id="pokemon-psyduck-widget" style="float:left"></div>
<div id="pokemon-slowpoke-widget" style="float:left"></div>
<div id="pokemon-bulbasaur-widget" style="float:left"></div>

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