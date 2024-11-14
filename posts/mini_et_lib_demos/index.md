# MiniEtLibDemos


提取自ET 7.2，仅保留核心框架层，相关业务层基本都被剔除，便于用来理解学习

{{&lt; link href=&#34;https://github.com/YaoXuanZhi/MiniEtLibDemos&#34; content=&#34;MiniEtLibDemos&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

&lt;!--more--&gt;

这个仓库是本人在项目接入ET框架后，出于推广学习需要搞的ET精简版，剔除了ET上的各种业务逻辑，仅仅保留了ET框架的核心ECS模块、事件模块、网络模块以及消息模块等，同[AOGame](https://github.com/m969/AOGame)思路差不多，只是个人偏好不一样，并在此基础上搞了WinForm GUI Demo和UnityDemo等，完整版可以在[官网](https://github.com/egametang/ET)查看

---

### 目录结构
 - **AppClientCLI**：一个命令行客户端Demo
 - **AppClientGUI**：一个WinForm客户端Demo
 - **AppModel**：该程序集包含了各种与UI无关Component声明
 - **AppModelView**：包含了各种与UI相关Component声明
 - **AppHotFix**：该程序集存放了AppModel上的各种Component的方法
 - ~~**AppHotFixView**~~：该程序集存放了AppModelView上的各种Component的方法，这里由于WinForm的限制，已被合并到AppClientGUI/ModelVeiw上了
 - **AppServer**：一个命令行服务端Demo
 - **CLI_Loader**：用于加载CLI DLL的加载程序集
 - **Config**：放置配置表的地方，这里还集成了Luban工具
 - **GUI_Loader**：用于加载GUI DLL的加载程序集
 - ~~**MiniEtLib**~~：剥离出来的ET核心，位置已经挪到了`xxx\UnityDemo\Assets\Scripts\Core\MiniEtLib`
 - **SimpleBenchmarkDemo**：ET官方示例里的基准示例
 - **UnityDemo**：一个UnityDemo

## 碎碎念
该框架在项目落地之后，其信息量没变，只是代码组织形式由原本的OOP转变成了ECS了，不过它带来了一个好处，巧妙的程序集拓扑设计让它前后端都可以开发热重载，并且大大节省了团队内部自己对双端框架的演进时间，只不过，ECS带来的心智负担着实不小，在推进过程受到的阻力也有一些，但是凭着对热重载机制的喜爱，最终还是与组内伙伴集思广益，基于现有业务开发和后续的开发量产管线考虑，捋顺了它的开发流程和配套工具链

&gt;本人之前用过Erlang，也尝鲜过Flutter，它们的热重载机制都比较完善，开发体验一级棒，一图胜千言
![](/assets/2024-09-04/1725423344848.png)

最后再逼逼一句，无论何种框架，总会遇到众口难调的情况，作为团队的一员，没上升到可以做技术决策的地位之前，在技术方案选型阶段，多参与讨论，在方案落地时，多参与建设，这样于己于团队都是有利的，多种不同思想碰撞能带来技术视野、架构能力上的长进

&lt;!-- 所在项目出于可维护性和便于重构等等考虑，将前后端的开发而言统一到C#上了，而服务器早期是Skyent&#43;Lua，但是这个项目是一个重客户端项目，大伙受够了lua这类弱语言的重构灾难和调试困难，将skynet.dll导出接口给c#进行调用，并且在此基础上构建了初版NetCore服务器，网络层还是基于Skynet实现，后面熊猫入职，在项目引入ET Framework，我所在小组配合接入ET框架，这个框架有很多优点，具体看[官网](https://github.com/egametang/ET)介绍吧，由于本人主要职责是负责系统双端开发业务，而战斗、场景部分都是另外一个小组负责的，那边没有用到ET，所以实际上，ET Framework仅是在系统组上落地，领导看重它是一个相对成熟，经受过线上验证的基于C#双端框架，这样可以大大节省了团队内部自己对双端框架的演进时间，本人是出于开发效率考虑，对它提供的前后端开发热重载的机制很是喜欢，虽然需要将旧的OOP代码转换成ECS的结构相对麻烦(实际上组内反馈，早期心智成本确实高)，最终还是与组内伙伴集思广益，基于现有业务开发和后续的开发量产管线考虑，捋顺了它的开发流程和配套工具链 --&gt;

## 参考资料
 - [ET 7.2](https://github.com/egametang/ET/tree/release7.2)
   &gt;MiniET剥离自该版本
 - [X-ET7](https://github.com/IcePower/X-ET7)
   &gt;从这之中提取了Luban的整合代码
 - [AOGame](https://github.com/m969/AOGame)
 - [unity3d c#热重载-边运行边改代码](https://blog.csdn.net/koljy111/article/details/122162110)


---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/mini_et_lib_demos/  

