# 给扫描PDF添加OCR文本层 - OcrMyPdf


以前网络上收集到不少扫描版本的PDF文档，没有文字层，非常蛋疼，偶然发现了这个开源工具，非常推荐

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/ocrmypdf/OCRmyPDF&#34; content=&#34;OCRmyPDF&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

### Windows下安装

#### 安装Python依赖项
```sh
conda create -n pdf_env python=3.11
# 执行这一步，会将运行ocrmypdf的第三方包都一并安装好
pip install pip install ocrmypdf
```

#### 安装OCR等依赖项
```sh
# 以管理员身份运行cmd，执行以下命令：
@&#34;%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe&#34; -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command &#34;[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString(&#39;https://community.chocolatey.org/install.ps1&#39;))&#34; &amp;&amp; SET &#34;PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin&#34;

# 或者以管理员身份运行PowerShell，执行以下命令：
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString(&#39;https://community.chocolatey.org/install.ps1&#39;))

choco install --pre tesseract
choco install ghostscript
choco install pngquant
```

由于默认安装tesseract只包含了English语言包，要想支持多种语言的话，从[tessdata](https://github.com/tesseract-ocr/tessdata/)上下载其它语言包，解压里面的扩展名为traineddata的文件，复制到 `C:\Program Files\Tesseract-OCR\tessdata` 上

### 运行
```sh
ocrmypdf -l chi_sim --pdf-renderer tesseract --output-type pdf source.pdf ocr.pdf
```

### 源码调试
由于官方忽略了.vscode等ide配置，所以我fork了仓库，并将vscode的调试配置放在learn分支下

```sh
git clone https://github.com/YaoXuanZhi/OCRmyPDF
git checkout learn
```

用vscode打开仓库根目录，F5运行调试即可

### 后记
之所以想要源码调试它，是想从其之中汲取到添加OCR文本层的思路，后续会将这块的分析整理成博客分享

### 参考资料
 - [关于win10原生系统下 OCRmyPDF安装使用](https://www.cnblogs.com/edisp/p/16667455.html)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/ocrmypdf/  

