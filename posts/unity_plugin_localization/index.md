# 一种通用的Unity插件本地化思路


最近在逛 [Unity资源商城](https://assetstore.unity.com/) 的时候发现，好多插件都没做多语言支持，用起来比较蛋疼，在项目组里不好推广。为此捣鼓了半天，总算整出一套相对通用的方案，发出来给大伙参考～

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/YaoXuanZhi/UnityPluginLocalization&#34; content=&#34;UnityPluginLocalization&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

### 前言
近段时间，精挑细选了一些Unity插件整合到项目里，而在此过程中，遇到了一些相对尴尬的事情，举个例子，新引入的一些插件更多是面向策划、美术的，但它们的Editor本身没有提供中文支持（绝大部分只有英文、日文等情况），实在太不友好了，因此萌生了一个想法，为这些插件新增一个多语言机制支持

#### 方案实现
阅读了一下市面上有多语种支持的Unity插件源码(比如 `ThunderFire UXTool`)之后，整理出了以下思路：
  - 提供一个多语言配置文件格式，比如json、xml等，用来存储不同语言的文件
  - 多语言配置文件的存储位置应该位于该插件的相对目录下
  - 提供一个多语言切换的接口，让插件来切换当前显示的语言，支持配置默认语言，使用EditorPrefs来实现修改持久化
  - 借鉴自Qt的思路，提供一个类 `Tr()` 接口，用来快速获取当前text_key的多语言文本
  - 提供一个`LocalizationProvider.OnGUI()`接口，让其在待支持的插件设置界面里新增一个语言设置项

#### 工作流程

1.  **初始化**: 编辑器或插件窗口启动时，`LocalizationManager` 会根据 `EditorPrefs` 中存储的语言（或默认语言）调用 `LoadLanguage`，加载对应的 JSON 文件到内存中的字典里
2.  **UI 刷新**: 插件的各个部分通过调用 `LocalizationManager.Tr(&#34;some_key&#34;)` 来获取需要在 UI 上显示的文本
3.  **用户切换语言**:
    1. 用户在 `LocalizationProvider` 提供的下拉菜单中选择一门新语言
    2. `LocalizationProvider` 检测到变更，调用 `LocalizationManager.SetLanguage` 并传入新的语言代码
    3. `LocalizationManager` 更新 `EditorPrefs` 中的语言设置
    4. `LocalizationManager` 立即调用 `LoadLanguage`，清空旧的语言字典，并加载新语言的 JSON 数据填充字典
4.  **UI 更新**: 在下一次 `OnGUI` 循环中，所有调用 `LocalizationManager.Tr()` 的地方都会自动从更新后的字典中获取新的文本，从而完成界面的语言切换

#### 效果图
![](/assets/2025-09-01/1756657689304.png)

---

### 参考资料
 - [ThunderFire UX Tool](https://uxtool.netease.com/)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/unity_plugin_localization/  

