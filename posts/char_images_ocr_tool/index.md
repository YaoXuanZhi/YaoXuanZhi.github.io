# 字符图片OCR工具


&gt;本人在收集一些文章正文的时候，中途被一些采用图文混合形式加密保护的网页卡着了，这篇文章记录下了我针对该类网页的处理方式

---

### 前言
由于自己的某些学习需求，不想一直联网浏览某些网页内容，因此想将这些站点的某些内容缓存到本地。另外，该网站为了防止内容*被爬虫抓取*或*被盗稿*，因此在页面渲染时对内容进行了一些加密处理：将纯文本内容拆分成**图文混合**形式渲染，格式如下所示：

```html
&lt;p&gt;生活就像海洋，只有意&lt;img src=&#34;/image/data/3452027561.png&#34; /&gt;坚强的人，才 &lt;img src=&#34;/image/data/0001716716.png&#34; /&gt;&lt;img src=&#34;/image/data/4356266290.png&#34; /&gt;达彼岸。 ——马克思
&lt;/p&gt;
```

分析了一下这个html源码，摸索出了它的加密规律：每个页面的字符图片都是随机的，也就是说，无法写一套`[图片名 =&gt; 字符]`的模板来批量替换，需要频繁对字符图片来进行OCR识别

但目前的免费OCR服务，都有体验次数限制，毕竟识别精度一般较高，而且维护这套识别服务也是挺费钱的。而按照本人的估计，假设要全部缓存这个站点的内容，采用在线OCR服务来识别这些字符图片肯定要凉，当天免费次数铁定不够；那么，相对靠谱的方案就是**自建OCR服务**或者搞个**Native OCR工具**来做OCR识别了，这些方案没有商用服务的体验限制

### OCR开源技术调研
思路确定了，但是面临了一个新问题，本人并没有OCR相关的开发经验，只能采用开源OCR项目来达成自己的目的，在网上检索一轮之后，找到了这些方案：

 - pandaocr
   &gt;在线ocr服务的聚合工具，实际上还是有体验次数限制
 - chineseocr-lite
   &gt;识别准度可靠，需要自行编译，windows下部署环境相对复杂，费时间，服务方式调用
 - tesseract
   &gt;这个开源方案虽然是最有历史的，部署简单，但是官方提供的模型数据给出的识别效果最差，耗时最多，要想获得较高的识别精度，需要自己花费非常多精力去调教，或者尝试其它训练模型，是故不推荐
 - paddleocr
   &gt;它是百度开源ocr项目，基于paddle深度学习框架实现的，部署难度一般，国内资料非常丰富，并且整个项目非常庞大，使用官方提供的模型数据即可有较高识别精度，但是耗时一般，对于复杂的文字排版的识别结果可用。如果有识别各种广告营销图的OCR需求，那么它将是目前较好的开源方案
 - cnocr
   &gt;它是起源于[爱因互动 Ein&#43;](https://einplus.cn/) 内部的项目需求，后续开源出来，其部署简单，使用官方提供的模型数据即可有较高识别精度，并且耗时最少，对于复杂的文字排版的识别结果准度一般。如果只是单纯的文字图片识别，比如微信聊天截图啥的，那么它的识别准度和识别速度都是较好的，但是不建议使用它来识别广告营销图

本人在Windows系统下分别跑通了tesseract、cnocr、paddleocr这些开源项目，以下是本次技术调研的总结：

|  OCR方案   | 部署难度  | 文字图片识别精度 | 复杂文字排版识别精度 | 识别速度 |
|  :----:  | :----: | :----: | :----: | :----: |
|  tesseract | 简单 | 较差 |  不可用  | 慢 |
| cnocr | 简单 | 准确  |  勉强能用  | 快 |
| paddleocr  | 复杂 | 准确  | 可用  | 一般 |

经过调研对比，最终采取cnocr作为字符图片的ocr方案

&gt;其实在windows下，Tim的`Ctrl&#43;Alt&#43;O`快捷键呼出来的`[相框识别文字]`的识别精度更高

### 设计识别流程
在调研过程中，发现每次ocr识别耗时是秒级的，并且字符图片的识别准度不如合并后的图片识别准度，为了提升最终的ocr识别准度以及减少耗时，需要先合并多个字符图片，再进行ocr识别，流程图如下所示：

![字符图片OCR识别](/assets/2021-04-25/char_images_ocr_tool_flow.svg)

### 开发思路
确定具体的OCR方案后，梳理此工具的实现思路，大致步骤如下：
  1. 使用wget来爬取这个网站的资源 `wget -c -r -np -k -p -nc &#34;http://www.xxx.com/xxx/&#34;`
    &gt;Windows下，建议使用msys2来执行
  2. 识别该站点里的字符图片文件夹，将ocr识别结果按照`[字符图片=&gt;字符]`的映射关系导出为json文件
  3. 读取json文件里面的字符图片映射数据，将html里面的字符图片标签替换成正常的字符
  4. 继续执行正常的网络爬虫

### 成品

- 功能特性
  - [x] 添加帮助文档
  - [x] 支持合成单行文字图片
  - [x] 支持合成多行文字图片
  - [x] 支持可选调试信息输出
  - [x] 支持可选保存合并后的图片
  - [x] 支持cnocr识别，并将映射结果导出到json
  - [x] 支持外部OCR结果校正
  - [x] 直接替换html里的字符图片标签
  - [x] 增加Windows右键菜单集成支持

附件：[char_images_ocr_tool.zip](/assets/2021-04-25/char_images_ocr_tool.zip)

---

### 参考资料
 - [字符图片合并](../char_image_merge/)
 - [初探cnocr](../cnocr/)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/char_images_ocr_tool/  

