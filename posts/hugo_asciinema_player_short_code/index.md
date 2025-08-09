# Hugo AsciinemaPlayer ShorCodes


考虑到现在各种网站可能会不再维护的情况，直接采用分享Id的方式并不能保证链接一直生效，因此希望shortcodes可支持asciinema可以传入相对文件路径来加载显示

<!--more-->

{{< link href="https://github.com/asciinema/asciinema" content="asciinema" title="documentation of FixIt Theme" card=true >}}

本人将个人博客更换到Hugo之后，偶尔会使用到asciinema来存储控制台下的日志以及操作记录，而往上大部分的实现都是基于asciinema官网分享或者Hack网页主题来实现的，本人期望它是一个非侵入式的机制，因此开发了这个小工具

---

## 代码参考
```html
<div id="demo"></div>
<head>
  <link rel="stylesheet" type="text/css" href="/asciinema-player.css" />
</head>
<script src="/asciinema-player.min.js"></script>
<script>
  AsciinemaPlayer.create('/assets/2024-10-15/example.cast', document.getElementById('demo'));
</script>

<script
  async
  id="asciicast-550147"
  data-theme="solarized-dark"
  data-speed="1"
  
  src="https://asciinema.org/a/550147.js"></script>
```

>最终实现提取自[hextra](https://github.com/imfing/hextra)，让其适配了Fixit主题

{{< asciinema 
  file="casts/550147.cast"
  speed="1"
  autoplay="true"
  loop="true"
  markers="1.5:Installation,3.2:Configuration,5.8:Testing"
  poster="npt:0:1"
>}}

### 参考资料
 - https://whitefirer.org/posts/2022/08/28/hugo-asciinema-player/
 - https://github.com/whitefirer/iLoveIt/tree/master/assets/lib
   >这个作者自己搞了一个主题，但这并不是我想要的，我想要的是以一个插件拓展的方式来完成这个支持
 - https://fixit.lruihao.cn/documentation/content-management/shortcodes
   >这是是本人采取的主题，可以参考下它的做法来实现
 - https://gohugo.io/content-management/shortcodes/

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/hugo_asciinema_player_short_code/  

