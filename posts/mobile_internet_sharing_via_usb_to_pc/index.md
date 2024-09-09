# 手机通过usb线使用pc电脑上网


需要协助跟进一个真机上手游包特有的登录问题，初步定位到问题出在包体上，但该包在首次安装启动的时候，需要下载10G&#43;的资源包，每次出包，资源包都会有变更，而公司的内网资源服务器空间有限，并且wifi信道存在多人相互抢占的情况，最终导致资源下载特别慢，为了排查这么个问题，需要等待1h&#43;才能进游戏；如果让手机通过usb线使用pc电脑上网，这样就能规避上述问题了，最终借助了[gnirehtet](https://github.com/Genymobile/gnirehtet)来达成目的

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/Genymobile/gnirehtet&#34; content=&#34;gnirehtet&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

##  使用步骤
 - 手机端准备
   - 在手机上进行以下步骤：`【打开开发者选项】=&gt;【打开USB调试】=&gt;【打开USB安装】=&gt;【打开USB调试（安全设置）】`，以下以小米手机为例截图说明：
     &gt;![](/assets/2024-09-04/1725425197770.png)
 - 电脑端准备
   - 第一步：将 `\\server1\软件安装包\自助出包相关\gnirehtet-toolkit.zip` 拷贝到本地并解压
     &gt;![](/assets/2024-09-04/1725425212506.png)
   - 第二步：准备好一条可以支持数据传输的usb数据线，并且将电脑与手机连接，并选择`【文件传输模式】`
   - 第三步：运行`gnirehtet-run.cmd`命令即可，顺利的话，此时手机会收到推送过来的`gnirehtet.apk`让你提示安装，信任它并允许安装即可，该app安装上手机后，此时

![](/assets/2024-09-04/1725425222226.png)

## 参考资料：
 - [教你如何将手机通过USB连接的方式 共享电脑高速稳定的网络](https://zhuanlan.zhihu.com/p/584736445)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/mobile_internet_sharing_via_usb_to_pc/  

