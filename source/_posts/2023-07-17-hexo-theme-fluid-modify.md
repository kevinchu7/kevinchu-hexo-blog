---
title: Hexo博客Fluid主题魔改记录
index_img: https://static.kevinchu.top/blog/assets/img/cover_048.jpeg
date: 2023-07-17 00:36:47
updated: 2023-09-14 10:50:30
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

展示位置随意，比如放在博客底部信息里，在主题配置项```footer.content```里追加：
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


## 5.导航栏标题添加霓虹灯特效
修改```themes\fluid\source\css\_pages\_base\_widget\header.styl```，在里面追加样式(注意缩进)：
```
.navbar-title
  outline none
  --c lightseagreen
  text-shadow 0 0 10px var(--c),0 0 20px var(--c),0 0 40px var(--c),0 0 80px var(--c),0 0 160px var(--c)
  animation animate 5s linear infinite

@keyframes animate{
  to{
      filter: hue-rotate(360deg)
  }
}  
```
然后修改```themes\fluid\layout\_partials\header\navigation.ejs```，
找到对应导航栏标题的标签内容：
```<strong><%= theme.navbar.blog_title || config.title %></strong>```
给其添加上class属性，修改为：
```<strong class="navbar-title"><%= theme.navbar.blog_title || config.title %></strong>```


## 6.导航栏添加和风天气插件
先去[和风天气官网](https://widget.qweather.com/)创建一个天气简约插件的代码（需要注册账号），插件样式可根据需要自定义，生成过的插件信息也可在控制台查到

然后修改`themes\fluid\layout\_partials\header\navigation.ejs`：
找到导航栏标题`a`标签（`class`属性为`navbar-brand`），在其下方添加刚刚生成的天气插件代码，同时修改下，将天气插件代码第一行的`div`标签用`span`标签包裹起来，并设置一个`class`属性比如`weather-plugin`：
```html
    <span class="weather-plugin"><div id="he-plugin-simple"></div></span>
    <script>
        WIDGET = {
            "CONFIG": {
                "modules": "10243",
                "background": "5",
                "tmpColor": "FFFFFF",
                "tmpSize": "16",
                "cityColor": "FFFFFF",
                "citySize": "16",
                "aqiColor": "FFFFFF",
                "aqiSize": "16",
                "weatherIconSize": "24",
                "alertIconSize": "18",
                "padding": "10px 10px 10px 10px",
                "shadow": "0",
                "language": "auto",
                "borderRadius": "10",
                "fixed": "false",
                "vertical": "top",
                "horizontal": "left",
                "key": "your key"
            }
        }
    </script>
    <script src="https://widget.qweather.net/simple/static/js/he-simple-common.js?v=2.0"></script>
```

修改`themes\fluid\source\css\_pages\_base\_widget\header.styl`，在里面追加对应的样式代码（限制可见宽度）：
```css
.weather-plugin
  @media (max-width: 992px)
    display none
  @media (min-width: 992px)
    display block
```


## 7.替换Mac风格代码块
在主题文件`themes\fluid\source\css`目录下新建样式文件，如`macpanel.styl`，内容参考：
```css
.highlight
    background: #21252b
    border-radius: 5px
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, .4)
    padding-top: 30px

    &::before
      background: #fc625d
      border-radius: 50%
      box-shadow: 20px 0 #fdbc40, 40px 0 #35cd4b
      content: ' '
      height: 12px
      left: 12px
      margin-top: -20px
      position: absolute
      width: 12px
```

修改主题配置文件`_config.fluid.yml`，找到`custom_css`配置项，引入刚刚新建的样式文件（此处引入`.styl`文件无需加后缀）：
```yml
custom_css:
  - /css/macpanel
```

继续修改`_config.fluid.yml`，找到`code.highlightjs`配置项，将代码高亮风格修改暗色风格（dark系列），如：
```yml
    highlightjs:
      # 在链接中挑选 style 填入
      # Select a style in the link
      # See: https://highlightjs.org/static/demo/
      style: "github dark dimmed"
      style_dark: "dark"
```
>注：如果选用其他代码高亮风格，有可能会造成代码背景是偏白色的，与黑色的Mac栏不搭。


## 8.添加礼花打字特效

主题配置项```custom_js```引入[typing-effect.js](https://static.kevinchu.top/blog/assets/js/typing-effect.js)
