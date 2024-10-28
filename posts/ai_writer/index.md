# AI写作尝鲜


从识字以来，已经遭遇了不少烂尾或太监的网络小说，每次遇到，都有很强的动力去想将它续写或者改变它虎头蛇尾的后半段，但是本人的写作水平一般，短篇都未曾驾驭过，更不用说那种动辄几百万字的网络小说，一直没去做，当然也是没那个闲工夫啦。幸运的是，现在在OpenAI等科技巨头的带动下，涌现了很多AI工具，其中就包括一些AI写作应用，ChatGpt4 和通义千问等问答语言模型。这些工具为写作提供了强大的辅助支持。在下面，我将整理下AI_Writer 的开源 AI 写作工具的试用体验

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/BlinkDL/AI-Writer&#34; content=&#34;AI-Writer仓库地址&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

#### 环境部署
基于anaconda3的集成环境

```sh
# 创建独立的python3运行环境
conda create -n ai_writer python=3.8
conda activate ai_writer

# 安装相关依赖包
pip install torch==1.9.1
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install numpy
pip install onnxruntime
```

#### 克隆仓库 
```sh
git clone https://github.com/BlinkDL/AI-Writer
```

### 模型部署
![](/assets/2022-01-06/1641484352617.png)

![](/assets/2022-01-06/1641484403827.png)

### 运行Demo
修改run.py的默认配置，官方默认为gpu运行模式，其运行环境配置复杂，尝鲜还是采用cpu模式更简便

![](/assets/2022-01-06/1641484761493.png)

```sh
cd AI-Writer
python run.py
```

### 参考资料
 - [开源：小说 AI 网文写作（支持N卡A卡I卡和CPU）有玄幻和言情模型](https://zhuanlan.zhihu.com/p/423646620)
 - [炼丹分享：AI 写网文小说的 GPT 模型（100M参数量），以及一种新采样方法](https://zhuanlan.zhihu.com/p/394766831)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/ai_writer/  

