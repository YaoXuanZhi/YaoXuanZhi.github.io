# 谈谈Win10系统的折腾


&gt;最近入手了台Surface Pro6，然后又重新捣弄起各种常用的开发环境了，C&#43;&#43;、Python、Php的开发环境搞起等，不过很快就遇到了各种状况，这里特此记录下来，以前很多笔记都丢到有道云笔记上了，这里重新拾遗吧。

## 触摸板无法正常拖动文件和截图了
其实这个在Win 10里面是可以设置的，通过**开始菜单**  --&gt; **设置** --&gt; **设备** --&gt; **触摸板**，然后勾选 **☐点击两次之后再拖动及可多选** 即可，如下图所示：
 - ![](/assets/2019-05-08/1557245985050.png)

&gt;另外，Win10内置的触摸板手势也可以在这个设置界面上看到哦O(∩_∩)O

## 无法在第三方应用上打开浏览器
&gt;比如QQ、VSCode里面的url，点击之后无反应

这是因为url的默认打开程序被设置成无效程序了，可能因为之前的程序被卸载导致的，重新指定一个即可

## 一些应用无法正常添加到右键菜单，比如TortoiseGit、TortoiseSvn、Cmder
一般是以下两种情况：
 - 没有以管理员身份运行，比如Cmder
 - 没有安装与Windows 系统对应的32bit/64bit版本应用，举个例子，TortoiseGit 32bit是无法在Win10 64bit上添加上右键菜单的

&gt;~~如果是电脑小白的话，其实还有一种是被安全管家类软件拦截导致的，这里就不赘述了==!~~

---

### 更改TortoiseSVN的文件比较工具
由于未知名原因，**TortoiseIDiff**工具无法在`SVN Commit对话框`上直接打开，因此将文本比较工具由 **TortoiseIDiff**更改为`Beyond Compare 3`，

&gt;打开TortoiseSVN的Setting，选择左边的 **Diff Viewer**，然后设置Diff configure，如：`&#34;...\BComp.exe&#34; %base %mine /title1=%bname /title2=%yname /leftreadonly`，配置如下所示：
 - ![](/assets/2019-05-08/1557671439648.png)

参考资料：
 - [TortoiseSVN比较工具设置为BeyondCompare 4](https://www.cnblogs.com/linlf03/p/6923519.html)

---

# 吐槽
这个价位下的surface pro已经没啥性价比了，但是作为一枚伪软粉以及颜控党，终究充值了一波信仰（ps:真香定律无处不在啊），另外它的便捷性的确比普通笔记本好多了，作为一个后端狗，算是为自己的背包减肥了。


---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://example.org/posts/about-deploy-win10-environment/  

