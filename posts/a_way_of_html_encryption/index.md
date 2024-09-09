# 谈一种页面加密技术


在一次练车的时候突发奇想，发现自己有些私密分享的需求，然后有了这次技术探索

&lt;!--more--&gt;

---

### 前言
这段时间边练车边整理工作笔记，然后突然发现，有一些内容不适合直接公开分享，但是也希望发布出去，被那些想看的亲朋好友可以正常浏览，因此有了html的加密访问需求，另外，考虑到这些加密分享的内容并不适合被搜索引擎检索到它的正文，所以正文内容也是有加密需求的。于是就在网上检索有没有相关的技术实现的文章。然后找到了这些技术博客或在线工具：
 - [HTML的简单页面加密代码](https://blog.csdn.net/weixin_44548876/article/details/86725023)
   &gt;基本流程上是没啥问题，但是这个示例里面，密码完整验证的方式已经被明文存储在页面上了，纵使密码存放在其它地方，采用Ajax方式获取之后再对比也毫无意义，开发者很容易在浏览器上通过F12修改这个密码验证逻辑从而正常浏览到，并且，由于这里面的内容也是明文存放着的，也会被搜索引擎检索到
 - ~~[HTML网页加密解密](http://cha.buyiju.com/tool/cryptogram.htm)~~
   &gt;无法正常体验，并且访问体验也不是我想要的

总而言之，虽然找到了各种貌似可用的实现，但是都没有get到我的需求痛点，决定自己开搞一个

### 基本思路
实际上，本人对访问的加密强度没啥追求，只需要以下特性即可：
  - *正文不能被搜索引擎爬虫到*
  - *需要输入密码访问*

针对这两个需求点，采用以下两种加密方式混合使用即可达到：
  - **base64** (一种可逆加密方式，将明文或字节流转换成base64字符串，在此解决首次xor加密生成的字节流存储在页面的问题)
  - **xor** (一种可逆加密方式，提供密钥验证支持，在正确的密钥下，进行两次该加密即可获得原文)

#### 页面加密打包思路
读取需要加密的html文件的特定标签内容，将其进行base64&#43;xor加密之后，塞到一个可以进行密码验证访问的模板标签上，删掉这个已加密的正文标签后，将模板标签再重新拼接成新的html。具体流程流程看代码啦，也是比较直观的

#### 页面验证访问思路
游客打开这个加密后的html之后，已加密的标签上会显示一个密码验证的界面，如果密码输入错误，浏览器会弹框提醒，如果输入密码正确，则解密后的正文标签outHtml会直接替换掉这个密码验证标签的outHtml，此时浏览器会自动将正文内容加载出来

#### 密码验证思路
核心思路是提供一个 **前缀字符串(prefix_str)** 来作为判断依据，最终整个流程如下所示：

 - 加密打包：`base64(xor(prefix_str&#43;content))`
 - 解密验证：`xor(unbase64(encode_str))`，再从中匹配前缀字符串是否刚好为 **prefix_str**，如果是，则密码正确，反之，提醒用户重新输入

#### 加密/解密思路
从html上提取到需要加密的正文tag之后，其加密、解密的流程设计如下所示：

```text
           html encrytion                        html decrytion

          &#43;--------------&#43;                      &#43;--------------&#43;
          |              |                      |              |
          |input html tag|                      |input html tag|
          |              |                      |              |
          &#43;--------------&#43;                      &#43;--------------&#43;
                  |                                     |
                  |                                     |
                  |                                     |
          &#43;-------v--------&#43;                  &#43;---------v---------&#43;
          |                |                  |                   |
          | xor encryption |                  | base64 encryption |
          |                |                  |                   |
          &#43;----------------&#43;                  &#43;-------------------&#43;
                  |                                     |
                  |                                     |
                  |                                     |
         &#43;--------v----------&#43;                  &#43;-------v--------&#43;
         |                   |                  |                |
         | base64 encryption |                  | xor encryption |
         |                   |                  |                |
         &#43;-------------------&#43;                  &#43;----------------&#43;
                  |                                     |
                  |                                     |
                  |                                     |
           &#43;------v--------&#43;                     &#43;------v--------&#43;
           |               |                     |               |
           |output html tag|                     |output html tag|
           |               |                     |               |
           &#43;---------------&#43;                     &#43;---------------&#43;
```

#### 技术选型
此工具基于nodejs技术栈实现，经过对比，选择了下面这几个第三方库：
  - [js-base64](https://www.npmjs.com/package/js-base64)
    &gt;utf8支持良好的base64库
  - [cheerio](https://www.npmjs.com/package/cheerio)
    &gt;能够解析修改html的html库，多用于爬虫项目
  - [yargs](https://www.npmjs.com/package/yargs)
    &gt;能轻松配置命令，解析多个参数，并设置快捷方式等，还能自动生成帮助菜单的命令行辅助库

#### 代码实现
  - [base64&#43;xor加密解密库](https://github.com/YaoXuanZhi/base64_with_xor/blob/master/js/base64_with_xor.js)
  - [html加密打包工具](https://github.com/YaoXuanZhi/base64_with_xor/blob/master/tools/package_encrypted_html.js)

#### 后续迭代
由于目前本人的博客都是采用hexo发布的，因此需要将这个加密逻辑整合成hexo插件，期间找到了一款同类型的插件[hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)，这款hexo插件基本满足我最开始的要求了，但由于它是在我完成了[base64&#43;xor加密解密库](https://github.com/YaoXuanZhi/base64_with_xor/blob/master/js/base64_with_xor.js)和[html加密打包工具](https://github.com/YaoXuanZhi/base64_with_xor/blob/master/tools/package_encrypted_html.js)后，打算整合到hexo的时候才找到的，本着不断学习的态度，借鉴这个插件的思路，自己也重写了一个 OMG，感兴趣的可前往[hexo-encrypted-package-html](https://github.com/YaoXuanZhi/hexo-encrypted-package-html)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/a_way_of_html_encryption/  

