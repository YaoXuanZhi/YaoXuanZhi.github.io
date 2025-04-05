# Hugo AsciinemaPlayer ShorCodes


考虑到现在各种网站可能会不再维护的情况，直接采用分享Id的方式并不能保证链接一直生效，因此希望shortcodes可支持asciinema可以传入相对文件路径来加载显示

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/asciinema/asciinema&#34; content=&#34;asciinema&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

本人将个人博客更换到Hugo之后，偶尔会使用到asciinema来存储控制台下的日志以及操作记录，而往上大部分的实现都是基于asciinema官网分享或者Hack网页主题来实现的，本人期望它是一个非侵入式的机制，因此开发了这个小工具

---

## 代码参考
```html
&lt;div id=&#34;demo&#34;&gt;&lt;/div&gt;
&lt;head&gt;
  &lt;link rel=&#34;stylesheet&#34; type=&#34;text/css&#34; href=&#34;/asciinema-player.css&#34; /&gt;
&lt;/head&gt;
&lt;script src=&#34;/asciinema-player.min.js&#34;&gt;&lt;/script&gt;
&lt;script&gt;
  AsciinemaPlayer.create(&#39;/assets/2024-10-15/example.cast&#39;, document.getElementById(&#39;demo&#39;));
&lt;/script&gt;

&lt;script
  async
  id=&#34;asciicast-550147&#34;
  data-theme=&#34;solarized-dark&#34;
  
  data-speed=&#34;1&#34;
  
  
  src=&#34;https://asciinema.org/a/550147.js&#34;&gt;&lt;/script&gt;
```

### 参考资料
 - https://whitefirer.org/posts/2022/08/28/hugo-asciinema-player/
 - https://github.com/whitefirer/iLoveIt/tree/master/assets/lib
   &gt;这个作者自己搞了一个主题，但这并不是我想要的，我想要的是以一个插件拓展的方式来完成这个支持
 - https://fixit.lruihao.cn/documentation/content-management/shortcodes
   &gt;这是是本人采取的主题，可以参考下它的做法来实现
 - https://gohugo.io/content-management/shortcodes/

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/hugo_asciinema_player_short_code/  

