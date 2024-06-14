# Hexo插件-文章加密


之前在[《谈一种页面加密技术》](../a_way_of_html_encryption/)里谈到了自己捣弄了一个页面加密方案，并且也开发了一个cli工具用来对html或tag进行打包加密，由于本人的文章都是采用hexo来发布的，因此就需要将这个打包工具整合成hexo plugin了

&lt;!--more--&gt;

---

### 前言
这篇博客仅仅整理了本人开发这个加密插件的大体流程，由于互联网上关于Hexo插件的开发笔记早已汗牛充栋，在此就不再赘述了

### 使用方式
本人希望借助这款插件，只需要让那些想要加密的md的文件头上添加`password: your_pwd`，即可在执行`hexo g`的时候，自动对生成的html的文章正文进行加密

### 开发笔记
以下是本人在开发该插件时所检索到的有效资料和具体执行的插件学习&amp;开发流程：

 - 1. 了解hexo的插件工作机制
   - [插件|Hexo](https://hexo.io/zh-cn/docs/plugins.html)
 - 2. 找出处理文章正文的插件接口或示例
   - 在 **[hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)** 里找到了关键接口
 - 3. 新建一个插件项目
   - [《hexo教程之为hexo编写一个插件》](http://www.sumoon.com/hexo/hexo-simple-plugin/)
 - 4. 整合加密打包功能到这个插件项目上
   - 已上传到 **[hexo-encrypted-package-html](https://hub.fastgit.org/yaoxuanzhi/hexo-encrypted-package-html)** 仓库上了
 - 5. 将这款插件发布到[npmjs.com](https://www.npmjs.com)上
   - [《如何发布自己的NPM包（模块）？》](https://zhuanlan.zhihu.com/p/44156668)
 - 6. 将这款插件发布到hexo插件市场上
   - [插件|Hexo](https://hexo.io/zh-cn/docs/plugins.html)

### 插件调试
目前没有找到vscode直接断点调试hexo插件的方式以及相关的文章教程，这里采用的是根据`hexo.log()`或`console.log()`的日志输出来跟踪问题的

### 注意事项
由于GithubPage是部署在墙外的，部分国内的cdn国外无法正常访问，所以需将`bootstrap.min.css`的[国内url](http://cdn.bootstrapmb.com/bootstrap/4.4.0/css/bootstrap.min.css)要改成[国外url](https://getbootstrap.com/docs/4.6/dist/css/bootstrap.min.css)

### 后记
其实hexo 插件市场上也有好几款文章正文加密插件，其中一款也做得更加完善，具体看这里：[hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)，本人开发的此款加密插件可当作另一种实现思路

![](/assets/2021-03-25/1616647855692.png)

&gt;这些同类插件是在本人实现一遍页面加密技术之后才发现的，为了不让自己的折腾变成瞎折腾，因此硬着头皮在搞多一款同类插件出来了&gt;&lt;

---

### 参考资料
 - [插件|Hexo](https://hexo.io/zh-cn/docs/plugins.html)
 - [hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)
 - [《hexo教程之为hexo编写一个插件》](http://www.sumoon.com/hexo/hexo-simple-plugin/)
 - [《如何发布自己的NPM包（模块）？》](https://zhuanlan.zhihu.com/p/44156668)
 - [《谈一种页面加密技术》](../a_way_of_html_encryption/)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:54598/posts/how_to_write_a_hexo_plugin/  

