# 全球互联笔记


在2023年，由于各种AI应用的大爆发，简体中文互联网的高质量内容被各种量产垃圾内容掩盖住了，再叠加上各大日常App的大数据推荐系统，我们生活在一个个信息茧房中，为了避免坐井观天，需要整合国内外的高质量信息源，因此有了这篇笔记

&lt;!--more--&gt;

---

### Discord
推荐使用英国手机号，偶尔也可以使用国内手机号
 - [购买国外手机号的号码平台-freeNumbers](https://sms-activate.org/cn/freeNumbers)

大多活跃中的开源项目都会开通配套的Discord，对某个开源项目比较感兴趣的话，可以加入项目Discord里蹲一手消息

### Telegram
这是一个匿名 IM工具，有一些比较硬核的技术在这里分享传播着

### X(旧称Twitter)
这是一个各大政要，名人都会在此开通账号发表意见的地方，国际资讯的一手来源地

### 自建透明代理
#### 购买国外VPS
在上面部署一个SSR-Server，然后提供一个账号给自己的手机使用，并且在电脑和手机上安装v2ray应用，当然，这个vps上也可以部署一些其它服务，用来做一些尝试比较好

#### 在Vps部署SSR-Server
```sh
# [ssr-服务器介绍](https://git.io/v2ray.sh)
# 可以使用这个一键脚本
# bash &lt;(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)

# 本人用这个
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh &amp;&amp; chmod &#43;x ssr.sh &amp;&amp; bash ssr.sh
```

#### 使用SSR-Client
[ssr客户端介绍](https://clashv2ray.com/19945.html)

##### windows
推荐 v2rayN

##### android
推荐 v2rayNG

##### linux
推荐：v2raya，它自带了一个web设置后台，使用它不需要再手动设置linux的浏览器代理，在http://xxx:2017上导入ssr信息，并且设置好透明代理就行了
 - [v2raya installation](https://v2raya.org/en/docs/prologue/installation/docker/)

![](/assets/2024-09-09/1725892697051.png)


使用docker安装即可，由于国内已经ban掉了https://hub.docker.com/，目前虚拟机上要想正常安装使用docker，可以移步到这里看操作详情 [🤞](../debian_notes/#docker相关)

```sh
sudo mkdir /etc/v2raya
docker run -d \
  --restart=always \
  --privileged \
  --network=host \
  --name v2raya \
  -e V2RAYA_LOG_FILE=/tmp/v2raya.log \
  -e V2RAYA_V2RAY_BIN=/usr/local/bin/v2ray \
  -e V2RAYA_NFTABLES_SUPPORT=off \
  -e IPTABLES_MODE=legacy \
  -v /lib/modules:/lib/modules:ro \
  -v /etc/resolv.conf:/etc/resolv.conf \
  -v /etc/v2raya:/etc/v2raya \
  mzz2017/v2raya:latest

# curl http://127.0.0.1:2017
# 查询当前公网ip
# curl ifconfig.me
```

&gt; 如果群晖上也想有一个全局梯子(比如想下载youtube上的视频)，建议通过ssh进入群晖内部，也通过上面命令行来启动v2raya docker

一般来说，这上面会增加不少延迟，并且比单独在[justmysocks](https://justmysocks3.net/)的速度会差上一些的，这个就看vps的服务器质量了，
可以通过一个测速工具来进行实际测试，或者跑一个国外的测速网站

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/global_network_build/  

