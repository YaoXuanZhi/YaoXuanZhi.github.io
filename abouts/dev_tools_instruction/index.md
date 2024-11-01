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
     - [Github Copilot(VSCode/IntelliJ IDE) 收费](https://www.microsoft.com/zh-cn/microsoft-copilot)

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

## OCR识别
 - [PaddleOCR-多国语言文本识别](https://github.com/PaddlePaddle/PaddleOCR)
 - [CnOCR-中英文文本识别](https://github.com/breezedeus/cnocr)
 - [TexTeller-LaTeX识别](https://github.com/OleehyO/TexTeller)
 - [OcrMyPdf-给扫描PDF添加OCR文本层](https://github.com/ocrmypdf/OCRmyPDF)

## 美术资产
 - AI出图
   - StableDiffusion
   - [Flux](https://github.com/black-forest-labs/flux)
   - midjourny
   - 可灵
 - 图片格式转换
  - [SVGcode](https://svgco.de/)：将位图转换成svg图片
  - [图片转文本](https://meatfighter.com/ascii-silhouettify/spa/index.html#/)：将位图转换成文字艺术，类似Bug佛祖图片
 - Live2D
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

---

> 作者:   
> URL: http://localhost:1313/abouts/dev_tools_instruction/  

