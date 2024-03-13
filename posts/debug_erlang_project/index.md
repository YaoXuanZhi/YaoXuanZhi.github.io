# 调试Erlang工程


宇宙第一IDE，yyds

&lt;!--more--&gt;

 - [使用IDEA进行erlang的断点调试](https://blog.csdn.net/eeeggghit/article/details/106021723)
 - [intellij-erlang](https://github.com/yidayoung/intellij-erlang)

https://juejin.cn/post/7249381456048783419

https://juejin.cn/column/7248545082129874981

https://www.cnblogs.com/--00/p/erlang_tool_recon.html

我不是某个特定技术的布道者，它给我带来不了啥钱和正向收益，我应该选取的是一个尽可能简单和方便快捷上手的技术用来快速制作一款产品，然后上线，跟进用户反馈

在尽可能的境况下减少调试成本，加快开发调试效率，并且尽可能脱离系统底层细节，快速进入业务，让项目能够平稳上线

我并不过于关注细节，私以为我对自己个人的定位不是一个基础设施的建造者，我是在基础设施搭好之上不断组件业务所需要的各种组件和细节，将所需功能的支持给实现，设计得更加具有拓展性，提高团队协助的效率

当然，如果确实需要我改动底层，给我恰当的时间，我去钻研下大多也能够解决，我已经过了造轮子的时期了，目前更多时间是在于怎么提升自己业务上的开发效率和团队的开发效率上，这是我个人比较在意的事情，另外，怎么确保功能的尽可能少犯错和功能模块的稳定性也非常重要

使用UE的UMG开发一个2048小游戏，并且用Erlang开发一个服务器，Skynet也搞搞看，将整套开发方案，以及开发时热更新的开发思路全部打通

以后做一些个人独立项目就基于这套逻辑去将项目跑起来


### 源码安装rebar3


[rebar3官网安装指导]](https://rebar3.org/docs/getting-started/)

![安装rebar3成功后的设置项截图](/assets/2024-03-12/1710224129227.png)

需要将对应的`erl10.0.01/bin`目录添加到系统环境变量`path`上，另外，需要注意的是，假设你的erl是面安装版，那么需要手动执行一次 `` 来重定向Erlang版本的默认绑定路径

```bash
git clone https://github.com/erlang/rebar3.git
cd rebar3
bootstrap.bat
```


### idealC&#43;Erlang插件的开发流程
[【erlang】Intellij IDEA中 使用REBAR3配置教程](https://blog.csdn.net/B_uggggg/article/details/109343412)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/debug_erlang_project/  

