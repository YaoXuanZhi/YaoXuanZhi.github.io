# 通过rclone将Nas映射为网络磁盘(非局域网)


前段时间，剁手购买了群晖Nas，正在向仓鼠的路上狂奔，经过一番捣鼓后，Nas上已经囤了好几T的资源了，怎么便捷地访问这些资源呢？

&lt;!--more--&gt;

{{&lt; link href=&#34;https://rclone.org&#34; content=&#34;rclone官网&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

## 需求&amp;痛点
本人并不想借助WinScp之类的第三方软件的GUI界面来访问Nas资源，如果能做到像本地磁盘那样的访问体验就好了。实际上如果访问设备在同一局域网内，是可以通过SMB协议来达到类似效果，但是当人在外面，这种方式就莫得了

## 方案选型
经过一番检索，目前Nas的外部访问，提供了多种方式，如下：
 - SMB协议：这个协议是局域网共享文件专用的，只能内网使用，外部设备可以通过VPN服务器跳入到家庭局域网内，进行访问，需要额外搭建一个VPN服务器，其实这种方式还是比较安全的，通过ssh流程，而不是http流量，一般家庭宽带不会被封，但是本人在部署`LT2P/IPSE`的时候遇到了其他棘手问题，尚未解决，暂且不表
 - WebDav协议：基于Http协议的，将其包装成本地磁盘访问方式，因此检索到采用WebDav的方式共享到网络上，然后，在WebDav考虑到想要让多台办公设备都可以像访问本地磁盘那样访问了，目前发现RaiDrive，可以做到类似网络磁盘映射的效果，不过广告有点多，而且对第三方网盘的支持得付费支持；还有一个就是rclone，这个就是本文的最终选型
 - 其他的就不再罗列了，详情可看群晖Nas的网络协议支持列表

## 动手
参考 [rclone-webdav](https://rclone.org/webdav/) 的配置，结合网上相关文章，这边做下统一整理

 - rclone的配置项放在`rclone.conf`文件上，其路径在`%APPDATA%/rclone/rclone.conf`上
 - rclone的webui需要额外从`github.com`上下载到本地磁盘上，其路径在`%LOCALAPPDATA%/rclone` 

进入Home/New Config上进行设置，选择WebDav，然后配置WebDav上的参数即可，
 - 默认端口：`http:5005`、`https:5006`
 - url：`http://myhome.yaoxuanzhi.xyz:5005`
 - 选择Owncloud
 - 用户名：`user_name`
 - 密码：`passwrd`
 - breatoken: `nas_config`

测试是否配置成功：罗列根目录:`rclone lsd nas:`
`./rclone.exe mount nas:/  M: --cache-dir D:Temp --vfs-cache-mode writes &amp;`

### Portable化处理
在摸索的过程中，为了方便重装电脑等情况下也能够快速部署回来，做了一定的资源整合，核心思路是：基于Windows系统的系统环境变量在程序间的传递关系链，将rclone用到的一些系统路径整合到rclone的运行目录上

 - 将 `%USERPROFILE%`、`%APPDATA%`、`%LOCALAPPDATA%` 都重定向到user目录里
 - 由于rclone挂载到系统磁盘上，需要有一个临时目录，将它也放在user同级目录下了

详情如下所示：

```sh
# set_rclone_env.bat
@echo off
set rclone_dir=rclone-v1.60.1-windows-amd64
set mine_root=%~dp0
set USERPROFILE=%mine_root%user
set APPDATA=%USERPROFILE%\AppData\Roaming
set LOCALAPPDATA=%USERPROFILE%\AppData\Local
set rclone_path=%mine_root%%rclone_dir%
set temp=%mine_root%temp

set path=%rclone_path%;%path%
```

基于这个思路，你也可以使用rclone来直接挂载OneDrive等第三方网盘

附件：[rclone_toolkit.zip](/assets/2022-10-26/rclone_toolkit.zip)

## FAQ
#### 为啥url填入了http://example.com依然无法正常访问，账号密码都是正确的？
检查你的WebDav服务的端口和端口转发端口，如果不是80端口，则需要在host上追加port信息，比如如果WebDav服务采用的是默认端口--http协议默认访问端口是5005、https协议默认访问端口为5006，此时url应该填：`http://example.com:5005`、`https://example.com:5006`

#### 如何指定WebDav根目录呢？
`rclone.exe mount conf_name(配置名):relative_path(根目录相对路径)`

## 参考资料
 - [Windows平台下使用 Rclone 挂载 OneDrive 为本地硬盘](https://www.lucktang.com/3435.html)
 - [使用Rclone实现网盘挂载](https://blog.csdn.net/qq_27387809/article/details/105443924)
 - [【超详细】Windows利用rclone将GoogleDrive等网盘/云盘挂载到本地](https://blog.csdn.net/weixin_42325659/article/details/102889983)

## 后记
本人在`LT2P/IPSE`碰了壁，但是后续还了解到存在其它虚拟组网技术，比如ZeroTier，SMB协议在这种虚拟局域网内也是可以使用的，如果双方设备可以成功进行P2P打通的话(最好双方设备有IPV6，这样P2P打通成功率极高)，基本可以打满家里宽带上行速度的上限，否则的话，得经过中转服务器做流量转发，这种体验就一般了

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/via_rclone_map_nas_as_network_disk/  

