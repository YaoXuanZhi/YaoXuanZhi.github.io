# 关于UE Lyra的Q&amp;A


公司高层做了一个从头再来的决策，团队由Unity3D引擎切换到UE5，我和一些小伙伴被留下来配合新大佬做UE Demo，这里记录了一些踩坑经历

&lt;!--more--&gt;

{{&lt; link href=&#34;https://www.bilibili.com/video/BV1Ce4y1X7k5&#34; content=&#34;UnrealCircle《Lyra初学者游戏包工程解读》 | quabqi&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

#### 看到一些开源UE项目，想知道它某个UI归属于哪个蓝图，怎么做？

使用`Tools -&gt; Debug -&gt; Widget Reflector(控件反射器)`工具，如下图所示：

![](/assets/2024-07-22/1721636235348.png)

![](/assets/2024-07-22/1721636540085.png)

 - [WidgetReflector官方介绍](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/Slate/WidgetReflector/)

#### Lyra上面的Esc键是如何进行响应的？

先在cpp代码里定义这个Esc按键对应的Tag，并在这里面编写它的逻辑，如下图所示：

![](/assets/2024-07-22/1721634491101.png)

在CommonUI Input Settings上添加这个Tag对应的键盘配置，如下图所示：

![CommonUI Input Settings上的Esc按键配置项](/assets/2024-07-22/1721634273883.png)

#### 在实际项目开发中，采用啥脚本语言？有啥优劣？
这次主程决定引入了Unlua，这个由tx内部维护并开源，但是目前该插件已经是搁置维护的状态了，但是好在它基本够用，坑相对较少，但是它对GameplayMessageSubsystem的支持不好，下图是Unlua官方群上的说法
![](/assets/2024-07-22/1721637290077.png)

&gt;早前还讨论过要不引入AngelScript，但是最终选了Unlua，私下了解到AngelScript在【双人成行】项目里被成功应用，但是国内目前是没有团队做这个事情的，所以选它风险是比较大的，不过它有一个好处，该团队改造了GameplayMessageSubsystem源码，针对AngelScript做了适配。不过还存在一个问题，就是它访问蓝图的变量这块做的一般，而且反射这块做得糙了些，没有Unlua那边做得那么齐全，并且官方也没有一个相对完整的可借鉴的项目示例(类似LyraWithUnLua)用来搭建新项目的Gameplay业务框架，举个例子：Gameplay的逻辑散布在cpp、蓝图、angelscript等脚本里，得自己去推导这整块流程，而且还无法借鉴Unlua的流程，属实不太方便

#### 参考资料
 - [Lyra跨平台UI开发(官方字幕)](https://www.bilibili.com/video/BV1mT4y167Fm)
 - [Lyra中AbilitySystem的应用 | Epic 陈宝康](https://www.bilibili.com/video/BV1we411N7qu)
 - [Lyra初学者游戏包概览(官方字幕)](https://www.bilibili.com/video/BV16B4y197Zy)
 - [Lyra导览与问答(官方字幕)](https://www.bilibili.com/video/BV1L94y197kh)
 - [分析Lyra中的动画(官方字幕)](https://www.bilibili.com/video/BV1yG4y187y6)
 - [UE5虚幻引擎5Lyra框架解析与扩展](https://www.bilibili.com/video/BV1XN411U7o1)
 - [UE5 CommonUI 框架 (一)](https://zhuanlan.zhihu.com/p/698935180)
 - [UE5 CommonUI 框架 (二)](https://zhuanlan.zhihu.com/p/700905273)
 - [【UE5】Lyra UI框架解析](https://zhuanlan.zhihu.com/p/609657844)
 - [虚幻5引擎上的脚本语言-AngelScript](https://zhuanlan.zhihu.com/p/567829611)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/question_and_answer_on_ue_lyra/  

