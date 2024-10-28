# 终端会话录制工具-asciinema/PowerSession-rs


发现了一个终端会话录制工具，非常不错，这里特意做下记录

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/asciinema/asciinema&#34; content=&#34;asciinema&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

### 使用场景
 - 特别适用于各种服务器部署使用等指导场景
 - 是演示各种cli工具使用的最佳方式，比起单纯的截图、视频录制更方便，它还能转换成svg动画，可以无损缩放，非常强大

### Linux下安装
看官方仓库ReadMe即可，这里不赘述

 - [asciinema](https://asciinema.org/)

### Windows下安装
由于官方asciinema实际上是不支持windows系统的，Github Issues上已有相关讨论，感兴趣的可以移步到这：
 - [Please clarify whether asciinema is expected to work on Windows #47](https://github.com/orgs/asciinema/discussions/47)
 - [Windows not supported, right? #47](https://github.com/asciinema/asciinema/issues/47)

有开发者采用Rust为其开发了一个Windows的兼容版本，该项目是[PowerSession-rs](https://github.com/Watfaq/PowerSession-rs)，安装教程非常简单，这里做下简单记录

```sh
winget install Watfaq.PowerSession
```

```sh
# 命令同asciinema完全一样，并且可以生成兼容的cast文件
PowerSession.exe -h

# 开启一个新终端会话，并开始录制，将结果保存到example.cast上
PowerSession.exe rec example.cast

# 回放example.cast上面的终端会话操作
PowerSession.exe play example.cast
```

### 配套工具介绍

#### svg-term 
这个工具在[OCRmyPDF](https://github.com/ocrmypdf/OCRmyPDF/tree/main/misc/screencast)上发现的，此项目将命令行操作过程录制下来，并将其转换成svg，放在ReadMe上，个人觉得效果非常惊艳，比转换成gif图片好多了，体积小，而且无损缩放

```sh
#创建NodeJs环境
conda create -c conda-forge --name nodejs_env nodejs
conda activate nodejs_env

# 安装
npm install -g svg-term-cli

# 使用示例
svg-term --in=misc/screencast/demo.cast --out=misc/screencast/demo.svg --window
```

### 演示效果
{{&lt; asciinema id=550147 theme=solarized-dark autoplay=false speed=1 &gt;}}

### 参考资料
 - [asciinema](https://asciinema.org/)
 - [PowerSession-rs](https://github.com/Watfaq/PowerSession-rs)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/asciinema/  

