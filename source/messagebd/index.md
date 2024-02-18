---
title: 留言板
banner_img: https://static.kevinchu.top/blog/assets/img/bg_025.jpg
comment: 'waline'
---

<style>
.page-content,
.post-content {
    overflow: hidden;
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

<script src="https://static.kevinchu.top/blog/assets/js/sakura-weak.js"></script>

<link
  rel="stylesheet"
  href="https://static.kevinchu.top/blog/assets/css/sakana-widget-2.7.0.css"
/>

<p style="text-align: center;">🍺欢迎！快来说点什么吧~</p>

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
    
    new SakanaWidget({ character: 'pokemon-pikachu', controls: false, rod: false }).mount('#pokemon-pikachu-widget');
    new SakanaWidget({ character: 'pokemon-psyduck', controls: false, rod: false }).mount('#pokemon-psyduck-widget');
    new SakanaWidget({ character: 'pokemon-slowpoke', controls: false, rod: false }).mount('#pokemon-slowpoke-widget');
    new SakanaWidget({ character: 'pokemon-snorlax', controls: false, rod: false }).mount('#pokemon-bulbasaur-widget');

  }
</script>

<script
  async
  onload="initSakanaWidget()"
  src="https://static.kevinchu.top/blog/assets/js/sakana-widget-2.7.0.js"
></script>
