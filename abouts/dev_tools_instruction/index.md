# 常用工具整理


在整个工作生活中，我们都扮演着各种角色，亦因此需要用到各种工具，比如需求分析、架构设计、技术方案讲解等等，这里记录了本人用到的一些常用工具

&lt;!--more--&gt;

## 日常工具
  - IDE
    - [VSCode](https://code.visualstudio.com/)
    - [IntelliJ IDE](https://www.jetbrains.com/idea/)

  - 截图&amp;标注软件
    - [ScreenPinKit](https://github.com/YaoXuanZhi/ScreenPinKit)
    - [Snipaste](https://www.snipaste.com/)
    - [PixPin](https://pixpinapp.com/)

  - 类ChatGpt
    - [kimi](https://kimi.moonshot.cn/)
    - [deepseek](https://www.deepseek.com/zh)
    - [ChatGpt](https://openai.com/chatgpt/)
    - [MiniCPM-V](https://github.com/OpenBMB/MiniCPM-V)
      &gt;多模态类ChatGpt工具，对标ChatGpt-V的功能，可以支持手机端部署

  - AI编程助手
     - [CodeGeex(VSCode/IntelliJ IDE) 免费](https://codegeex.cn/zh-CN)
     - [Github Copilot(VSCode/IntelliJ IDE) 收费](https://www.microsoft.com/zh-cn/microsoft-copilot)
     - [marscode豆包AI编程助手，类似Copilot](https://www.marscode.cn/home)
     - [screenshot-to-code 截图到代码，主要针对HTML](https://github.com/abi/screenshot-to-code)
       - [前端开发利器—图片生成代码](https://blog.fxcxy.com/2024/06/05/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91%E5%88%A9%E5%99%A8%E2%80%94%E2%80%94%E5%9B%BE%E7%89%87%E7%94%9F%E6%88%90%E4%BB%A3%E7%A0%81/)

  - 翻译工具
     - [PDFMathTranslate 科学 PDF 文档翻译及双语对照工具](https://github.com/Byaidu/PDFMathTranslate)
       &gt;沉浸式翻译也有类似效果，但是不够它好
     - [Chrome扩展-沉浸式翻译 为网页提供双语翻译](https://chromewebstore.google.com/detail/%E6%B2%89%E6%B5%B8%E5%BC%8F%E7%BF%BB%E8%AF%91-%E7%BD%91%E9%A1%B5%E7%BF%BB%E8%AF%91%E6%8F%92%E4%BB%B6-pdf%E7%BF%BB%E8%AF%91-%E5%85%8D%E8%B4%B9/bpoadfkcbjbfhfodiogcnhhhpibjhbnh)
       &gt;可以用在Discord上，但需要点击扩展图标才能正常翻译，或者开启悬停长按识别

  - 笔记应用
    - [logseq](https://logseq.com/)

  - 密码破解
    - [ciphey](https://github.com/Ciphey/Ciphey)
      &gt;docker run -it --rm remnux/ciphey ciphey -t &#34;aGVsbG8gbXkgbmFtZSBpcyBiZWU=&#34;

  - 域名注册
    - [namebeta](https://namebeta.com/)
    - [namecheap](https://ap.www.namecheap.com/)
      &gt;已购买

  - 自建博客
    - ~~[hexo](https://hexo.io/zh-cn/)~~
      &gt;nodejs各种第三方npm包依赖太重了，而且版本间兼容性差，已经改到hugo了
    - [hugo](https://gohugo.io/)
      &gt;由于互联网上的第三方文章链接可能会失效，如果遇到不错的文章，建议生成离线html文件保存到本地，本人在此采用 **简悦 - SimpRead Chrome插件**里的 `【动作】-&gt; 【导出为 离线 HTML】`功能来达成这一目的，它会将文章图片转换为base64编码，这样即使文章链接失效，各种图片也能正常显示

## 商业源码购买&amp;出售
 - [sellmyapp](https://www.sellmyapp.com/downloads/)


## 教学讲解
 - 算法演示
   - [Hello 算法](https://www.hello-algo.com/chapter_preface/summary/)
   - [算法运行演示](https://pythontutor.com/)

 - UML
   - [PlantUML](https://plantuml.com/zh/)
   - [drawio](https://www.drawio.com/)
   - [excalidraw](https://excalidraw.com/)
   - [asciiflow](https://asciiflow.com/)

 - 思想卡片
   - [napkin](https://app.napkin.ai/)
       &gt;[Napkin AI：这款免费AI工具，能把复杂的想法变成精彩的卡片，效率高到飞起！（Lisp提示词那样）](/assets/2024-10-15/simpread-Napkin%20AI：这款免费%20AI%20工具，能把复杂的想法变成精彩的卡片，效率高到飞起！（Lisp%20提示词那样）.html)

 - 开源IM
   - [tinode](https://github.com/tinode/chat)

## 游戏开发
 - [Unity](https://unity.cn/)
 - [UE](https://www.unrealengine.com/zh-CN)
 - [Godot](https://godotengine.org/)
 - 防破解
   - [unity 游戏源码防破解](https://zhuanlan.zhihu.com/p/720289641)
 - 防作弊
   - [pyod-异常行为检测库](https://github.com/yzhao062/pyod)
     &gt;对局内的玩家某段时间的操作行为记录，并使用该库进行分析，发现异常行为，进行封号、警告等处理

## OCR识别
 - [PaddleOCR-多国语言文本识别](https://github.com/PaddlePaddle/PaddleOCR)
 - [CnOCR-中英文文本识别](https://github.com/breezedeus/cnocr)
 - [surya-OCR、布局分析、读取顺序、表格识别](https://github.com/VikParuchuri/surya)
 - [TexTeller-LaTeX识别](https://github.com/OleehyO/TexTeller)
 - [OcrMyPdf-给扫描PDF添加OCR文本层](https://github.com/ocrmypdf/OCRmyPDF)

## 分布式存储技术&amp;应用
 - [ipfs-docs](https://docs.ipfs.tech/)
  - [libp2p-webrtc](https://docs.libp2p.io/guides/getting-started/webrtc/)
    &gt;基于这套技术，我们可以在浏览器中构建一个P2P网络，实现文件的共享和传输或者游戏联网
 - [御宅室](https://www.otakuroom.top/cn/index.html)

## 美术资产
 - AI出图
   - StableDiffusion
   - [Flux](https://github.com/black-forest-labs/flux)
   - midjourny
   - 可灵
 - 人物表情迁移
   - [HelloMeme：集成空间编织注意力，在扩散模型中嵌入高级别和保真度丰富的条件](https://github.com/HelloVision/HelloMeme)
     - https://songkey.github.io/hellomeme/
     &gt;可以将一些经典表情迁移到其它人物身上，并且不挑美术风格
 - 人物训练
   - [NovelAI-人物训练及炼丹embedding指南](https://www.bilibili.com/read/cv19336836/)
 - 图片格式转换
  - [SVGcode](https://svgco.de/)：将位图转换成svg图片
  - [图片转文本](https://meatfighter.com/ascii-silhouettify/spa/index.html#/)：将位图转换成文字艺术，类似Bug佛祖图片
  - [开源-将位图转换为svg图像](https://svgstud.io/main.html?ref=taaft&amp;utm_source=taaft&amp;utm_medium=referral)
 - Live2D
  - [live2d](https://www.live2d.com/)
  - [Live2Diff-通过单向视频扩散模型处理 Live2d 视频流的 Pipeline](https://github.com/open-mmlab/Live2Diff)
  - [头部说话演示](https://github.com/pkhungurn/talking-head-anime-demo)
    - [talking-head-anime-demo](https://www.bilibili.com/video/av91131149)
  - [如何使用Stable Difusion制作Live2D角色](https://www.youtube.com/watch?v=gdgvtaRJUNM)
 - 抠图
  - [BiRefNet-AI抠图](https://github.com/ZhengPeng7/BiRefNet)
  - ~~[batchtool](https://images.batchtool.com/zh)~~
    &gt;效率不高，不建议使用

## 影视后期
 - 剪辑类
   - [LosslessCut](https://github.com/mifi/lossless-cut)
 - 视频编码转换
   - [小丸工具箱](https://maruko.appinn.me/)
   - [Medlexo](https://sourceforge.net/projects/medlexo/)

## 开发环境搭建
##### Golang
```sh
# windows
# winget search golang
winget install GoLang.Go
```

##### Miniconda3
```sh
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe -o miniconda.exe
start /wait &#34;&#34; .\miniconda.exe /S
del miniconda.exe
```

 - [Miniconda](https://docs.anaconda.com/miniconda/)

##### Python
```sh
conda create -n python_env python=3.9
conda activate python_env
```

##### NoeJs

```sh
conda create -c conda-forge --name nodejs_env nodejs
conda activate nodejs_env
```

## 日常工具安装
#### windows
建议使用choco包管理进行安装即可，如果有其它偏好，下面有个对比图
![](/assets/2024-11-16/1731733693108.png)

```sh
choco config set installLocation D:\ProgramData
choco upgrade -y
```

```sh
choco install powertoys
choco install everythingpowertoys
choco install 7zip.install

# Scoop install powertoys
```

alt&#43;space=&gt;打开快捷搜索面板
win&#43;ctrl&#43;shitf&#43;t =&gt; 窗口裁剪和锁定
win&#43;ctrl&#43;t =&gt; 让某个窗口置顶
&lt;!-- win&#43;shitf&#43;t =&gt; 选取区域进行OCR识别，并且将结果复制到剪贴板 --&gt;
遇到某个文件被占用，无法删除，可以使用【使用文件锁定程序解锁】菜单项
按住Shift键，拖动窗口，重新排版屏幕窗口布局
ctrl&#43;space =&gt; 快速预览当前选中文件
选择多个文件【使用PowerRename重命名】
win&#43;ctrl&#43;` =&gt; 打开工作区

期望支持
支持设置窗口透明度 按住win&#43;ctrl&#43;滚轮时，可以往上或者往下设置窗口透明度

---

> 作者:   
> URL: http://localhost:1313/abouts/dev_tools_instruction/  

