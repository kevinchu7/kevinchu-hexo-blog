---
title: Hexo博客Fluid主题魔改记录
index_img: https://static.kevinchu.top/blog/assets/img/cover_048.jpeg
date: 2023-07-17 00:36:47
archive: true
tags:
    - Hexo
categories:
    - 博客建站
---

>这篇博客仅用于记录对本站使用的博客主题[**Fluid**](https://github.com/fluid-dev/hexo-theme-fluid)的魔改内容，以防遗忘。
---

## 1.添加鼠标移动小星星特效
主题配置项```custom_js```引入[stars.js](https://static.kevinchu.top/blog/assets/js/stars.js)

## 2.博客底部添加运行时间
修改```themes\fluid\layout\_partials\footer.ejs```，在第一行```<div class="footer-inner">```后面追加：
```javascript
  <div>
    <span id="timeDate">正在载入天数...</span>
    <span id="times">载入时分秒...</span>
    <script>
    var now = new Date();
    function createtime(){
        var grt= new Date("08/17/2020 00:00:00");
        now.setTime(now.getTime()+250);
        days = (now - grt ) / 1000 / 60 / 60 / 24;
        dnum = Math.floor(days);
        hours = (now - grt ) / 1000 / 60 / 60 - (24 * dnum);
        hnum = Math.floor(hours);
        if(String(hnum).length ==1 ){
            hnum = "0" + hnum;
        }
        minutes = (now - grt ) / 1000 /60 - (24 * 60 * dnum) - (60 * hnum);
        mnum = Math.floor(minutes);
        if(String(mnum).length ==1 ){
                  mnum = "0" + mnum;
        }
        seconds = (now - grt ) / 1000 - (24 * 60 * 60 * dnum) - (60 * 60 * hnum) - (60 * mnum);
        snum = Math.round(seconds);
        if(String(snum).length ==1 ){
                  snum = "0" + snum;
        }
        document.getElementById("timeDate").innerHTML = "🚀已持续航行&nbsp"+dnum+"&nbsp天";  
        document.getElementById("times").innerHTML = hnum + "&nbsp时&nbsp" + mnum + "&nbsp分&nbsp" + snum + "&nbsp秒";
    }
    setInterval("createtime()",250);
    </script>
  </div>

```


## 3.添加RSS链接

安装```hexo-generator-feed```插件:
```npm install hexo-generator-feed --save```

在hexo配置文件里追加配置项：
```yml
#Extensions
plugins:
    hexo-generator-feed
#Feed Atom
feed:
    type: atom
    path: atom.xml
    limit: 20
```

展示位置随意，比如放在博客底部信息里，在主题配置项```footer.content```里追加:
```<a href="https://blog.kevinchu.top/atom.xml" target="_blank" rel="nofollow noopener"><i class="iconfont icon-rss"></i>RSS</a>```


## 4.移动端支持显示回到顶部按钮
修改```themes\fluid\source\js\events.js```中的```registerScrollTopArrowEvent```，改为：
```javascript
function() {
    var topArrow = jQuery('#scroll-top-button');
    if (topArrow.length === 0) {
      return;
    }
    var board = jQuery('#board');
    if (board.length === 0) {
      return;
    }
    var posDisplay = false;
    var scrollDisplay = false;
    // Position
    var setTopArrowPos = function() {
      var boardRight = board[0].getClientRects()[0].right;
      var bodyWidth = document.body.offsetWidth;
      var right = bodyWidth - boardRight;
      posDisplay = right >= 50;
      topArrow.css({
        'bottom': scrollDisplay ? '20px' : '-60px',
        'right' : posDisplay ? right - 64 : 4 + 'px',
        'min-width' : posDisplay ? 40 : 20 + 'px',
        'min-height' : posDisplay ? 40 : 20 + 'px'
      });
    };
    setTopArrowPos();
    jQuery(window).resize(setTopArrowPos);
    // Display
    var headerHeight = board.offset().top;
    Fluid.utils.listenScroll(function() {
      var scrollHeight = document.body.scrollTop + document.documentElement.scrollTop;
      scrollDisplay = scrollHeight >= headerHeight;
      topArrow.css({
        'bottom': scrollDisplay ? '20px' : '-60px'
      });
    });
    // Click
    topArrow.on('click', function() {
      jQuery('body,html').animate({
        scrollTop: 0,
        easing   : 'swing'
      });
    });
  }
```
