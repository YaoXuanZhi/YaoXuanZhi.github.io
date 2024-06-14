# 自用工具-ScreenPinKit


本人之前比较常用Snipaste&#43;剪贴板OCR方案，但是随着时间推移，发现这套方案过于繁琐，并且想要一个屏幕标注功能，苦于Snipaste没有插件系统，并且市面上的屏幕标注工具没有用着舒服的，遂有了这个小工具

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/YaoXuanZhi/ScreenPinKit&#34; content=&#34;ScreenPinKit&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

### 前言
在多年工作中，逐渐将Snipaste&#43;离线ocr整合到自己的工作流上了，一般使用场景如下：

##### Snipaste的贴图功能使用场景(已满足)
 - 讨论需求细节的时候，通常会提前将要讨论的要点截图并贴到屏幕上
 - Review代码、追踪Bug的时候

##### OCR识别功能(基本可用)
 - 遇到防拷贝网页时
 - 观看视频时
 - 遇到PPT、Excel、PDF等文档时

得益于AI大爆发，目前一些常用IM应用(比如QQ、微信)上已内置了图片OCR功能，但一般办公电脑出于安全考虑从不登微信和QQ，为此捣鼓了一个离线OCR方案，它由CnOcr/PaddleOcr实现ocr识别，并结合AutoHotKey=&gt;uTools提供的双击Alt触发剪贴板OCR识别

虽然自己搞的这个剪贴板OCR方案可用，但是个人其实更喜欢微信图片OCR这种选择文本的交互方式，它更加无感一些。后续发现PixPin复刻了这个功能，而且OCR速度相当快

##### 屏幕标注(待满足)
后面发现在做教学讲解的时候，一个屏幕标注功能也变得不可或缺了，经过一番调研，ppInk、gInk、shareX等的操作要不过于复杂，要不界面过于老旧，逐渐有了这个小工具的需求雏形

##### 自用需求汇总
整体的截图、贴图体验参考Snipaste的交互方式，绘图体验则借鉴excalidraw，在截图标注、屏幕标注的时候共用同一套绘画操作逻辑，截图OCR识别则参考微信OCR功能，它最好能跨平台，易于维护拓展

### 方案选型
在将这些想法一一整理之后，在Flutter、Electron、Qt等技术上做了一次技术对比选型，但是一直不太满意

在我看来，易于拓展意味着开发环境尽可能简单，想做离线OCR识别离不开Python，所以迟迟没动手；直至看到了 [PyQt-Fluent-Widgets](https://github.com/zhiyiYo/PyQt-Fluent-Widgets) 项目的演示，一下子被它所吸引了，之后陆陆续续基于PyQt5复刻了Snipaste上自己常用到的功能，并且结合excalidraw的操作方式封装了一套绘图层和相关示例

|  技术方案   | UI风格  | 开发难度  | 拓展开发  | 是否支持开发热更新 | 是否已掌握 | 备注 |
|  :----:  | :----: | :----: | :----: | :----: | :----: | :----: |
|  Flutter | 较为现代化 | 一般 | 麻烦 | 支持 | 仅了解 | 得掌握一门Dart语言，该语言比较小众，收益比太低，AndroidStudio内置了开发热更新支持，易于开发GUI，但是跨平台开发比较费功夫，配置开发环境复杂，不利于其它贡献者参与 |
|  Qt | 相对硬核，但是结合QML机制，可以改得很好看 | 一般 | 麻烦 | 不支持 | 熟悉C&#43;&#43;，但不熟悉Qt框架 | 需要学习Qt框架，并且有多个Qt版本，网上资料丰富，但是配置Qt开发环境复杂，由于采用C&#43;&#43;开发，这会过滤掉很多贡献者 |
|  Electron | 较为现代化 | 一般 | 一般 | 不支持 | 有所耳闻 | 该方案会内置一个Web内核，过于重了，而且应用分发体积比较大，TypeScript比较少用，收益比太低 |
|  PyQt-Fluent-Widgets(PyQt5) | 较为现代化 | 一般 | 容易 | 支持，PyCharm安装Roadium插件即可 | 熟悉Python，但不熟悉Qt框架 | 它相当于一套FluentUI风格的PyQt5，而且Python易于学习上手，大多数开发者都掌握，另外以后若有商业化考虑可以无缝转译到Qt(C&#43;&#43;)上 |

### 后记
在这个过程中，也留意到有个[PixPin应用](https://www.appinn.com/pixpin/)，该作者的开发动机同本人蛮接近的，如下图所示：

![](/assets/2024-06-13/1718247982993.png)

经过一番对比后发现，他想做的东西无疑更大更全，它的截图OCR速度非常快，并且还支持滚动截图等方式，可谓是Snipaste的加强版，作者目前更新比较活跃。而我只是想要个自用的称手工具而已，很多高级特性根本用不上，因此本练手项目就继续推进了，另外，也给PixPin提了个屏幕标注的需求，希望能采纳吧

Python版本升级太快了，目前社区很多库跟不上，现在这个小工具的兼容性一般，在Python 3.8和Python 3.9上可以完美运行，在更高级Python版本上，system_hotkey包、pillow等第三方包相继出现了问题，最终兼容性等我空闲之余再处理了

这个练手项目里，会逐步将一些精华部分整理成文档放在doc目录内，有兴趣的童鞋可以用logseq加载该目录浏览；当然Snipaste还是有不少拿手绝活的，有一些机制我也暂且复刻不出来，但是不影响我目前的使用，以后随缘复刻吧，研究这个也挺有意思的

### 吐槽
本次练手项目是在一个陌生的技术栈上进行的，在这个过程中大量用到了类ChatGpt等AI工具辅助开发，经过对比，Kimi目前准确度是比ChatGpt3.5好上一些，这个小工具的一些源码和思路提取自网络，有一些是从c&#43;&#43;转译成PyQt版本的，总体上来说，效率确实有所提升，并且免去了各种广告，但是它只能解决一些常规性的问题，其无中生有的本事不小，需要我们仔细甄别；对于一些稍微专业性的需求开发，只能靠自己去摸索，拆解同类软件的实现思路了，至于具体用到哪些API，API使用示例之类的，这块倒是相信AI

虽然瑕疵不少，但是类ChatGpt的出现，确实大大降低了我们跨技术栈开发的难度了，毕竟有个初中级的助手在旁辅助给思路还是挺爽的，对于单体的战斗力提升帮助还是巨大的，常规CURD的末路已经来临了，各种低代码平台解决的常规问题会逐步被它替换掉，而专业性强的应用护城河还是存在的，而且会一直存在~~

经过这一番折腾，基本将PyQt需要踩的坑也过一遍了，逐渐熟悉了Qt框架；另外，随着对PyQt-Fluent-Widgets的熟悉，也逐渐发现了它的一些不足或者说Bug，后续给作者提个PR吧

---

### 参考资料
 - [辅助教学与文件注解利器｜5个免费又好用的屏幕画笔软件
在触摸屏上轻松手写涂鸦与注记](https://www.gechic.com/cn/best-free-annotation-software-tools/)
 - [**Snipaste**: Snipaste 是一个简单但强大的截图工具，也可以让你将截图贴回到屏幕上](https://zh.snipaste.com/)
 - [**excalidraw**: Design guidelines and toolkits for creating native app experiences](https://excalidraw.com/)
 - [**PyQt-Fluent-Widgets**: A fluent design widgets library based on C&#43;&#43; Qt/PyQt/PySide. Make Qt Great Again.](https://github.com/zhiyiYo/PyQt-Fluent-Widgets)
 - [**ShareX**: Screen capture, file sharing and productivity tool](https://github.com/ShareX/ShareX)
 - [**ppInk**: An easy to use on-screen annotation software inspired by Epic Pen.](https://github.com/onyet/ppInk/)
 - [**PixPin**: 功能强大使用简单的截图/贴图工具，帮助你提高效率](https://pixpinapp.com/)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:54598/posts/screen_pin_kit/  

