# Nginx笔记


Nginx在Web服务器上应用尤其广泛，这里是本人在使用过程中的一些笔记，方便以后查阅

{{&lt; link href=&#34;https://github.com/nginx&#34; content=&#34;nginx&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

&lt;!--more--&gt;

NGINX（读作：engine-x）是用于 Web 服务、反向代理、内容缓存、负载均衡、媒体流传输等场景的开源软件。NGINX 的创始人 Igor Sysoev 最初编写 NGINX 是为了解决 C10K 问题，如今，NGINX 已经成为全世界最流行的 web 服务器

---

### 安装部署

##### Linux
本人推荐通过docker镜像来安装，方便部署和迁移，以下是通过apt安装的步骤

```sh
# 安装Nginx
sudo apt update
sudo apt install nginx
```

```sh
# 添加 OpenResty 的官方仓库
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:openresty/ppa
sudo apt update
# 安装OpenResty
sudo apt install -y openresty
```

```sh
# 常用操作命令
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
```

##### Windows
 - [nginx download](https://nginx.org/en/download.html)
 - [openresty download](https://openresty.org/cn/download.html)

### 常用模板配置
##### 反向代理
```nginx
# nginx_reverse_proxy.conf
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

# 反向代理：VMware虚拟机里的web服务
http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  &#39;$remote_addr - $remote_user [$time_local] &#34;$request&#34; &#39;
                     &#39;$status $body_bytes_sent &#34;$http_referer&#34; &#39;
                     &#39;&#34;$http_user_agent&#34; &#34;$http_x_forwarded_for&#34;&#39;;

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen 8086;
        server_name web.yourdomain.com;

        location / {
            proxy_pass http://192.168.183.132:8086/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

    server {
        listen 8090;
        server_name admin.yourdomain.com;

        location / {
            proxy_pass http://192.168.183.132:8090/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

##### 负载均衡
```nginx
#nginx_load_banlancer.conf
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

# # 负载均衡：适合高并发查询服务
http {
    # upstream backend_servers {
    #     # 负载均衡策略：默认轮询

    #     server 192.168.183.132:8086;
    #     server 192.168.183.132:8090;
    # }

    # upstream backend_servers {
    #     # 负载均衡策略：加权轮询 &#43; 失败重试配置
    #     # 在fail_timeout时间内失败了max_fails次请求后，则认为该上游服务器不可用，然后将该服务地址踢除掉。fail_timeout时间后会再次将该服务器加入存活列表，进行重试

    #     server 192.168.183.132:8086 weight=3 max_fails=2 fail_timeout=60s;
    #     server 192.168.183.132:8090 weight=2 max_fails=2 fail_timeout=60s;
    # }

    upstream backend_servers {
        # 负载均衡策略：最小链接
        least_conn;

        server 192.168.183.132:8086;
        server 192.168.183.132:8090;
    }

    # upstream backend_servers {
    #     # 负载均衡策略：ip哈希
    #     ip_hash;

    #     server 192.168.183.132:8086;
    #     server 192.168.183.132:8090;
    # }
    
    # &lt;--------------------------------------------------&gt;

    # 针对某个http服务进行负载均衡
    server {
        listen 8020;
        server_name test.thinkshop.com;

        location / {
            proxy_pass http://backend_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

    # # 针对某个websocket服务进行负载均衡
    # server {
    #     listen 8030;
    #     server_name match.yourdomain.com;

    #     location / {
    #         proxy_pass http://backend_servers;
    #         proxy_http_version 1.1;
    #         proxy_set_header Upgrade $http_upgrade;
    #         proxy_set_header Connection &#34;upgrade&#34;;
    #         proxy_set_header Host $host;
    #         proxy_set_header X-Real-IP $remote_addr;
    #         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #         proxy_set_header X-Forwarded-Proto $scheme;
    #     }
    # }
}
```

### Nginx 高可用性保证
#### Linux
 - [Nginx&#43;Keepalived(双机热备)搭建高可用负载均衡环境(HA)](https://www.cnblogs.com/zxtceq/articles/14642657.html)
   &gt;[备份链接](/assets/2024-09-11/simpread-Nginx&#43;Keepalived(双机热备)%20搭建高可用负载均衡环境%20(HA).html)
 - [Nginx &#43; KeepAlived高可用负载均衡集群](https://blog.csdn.net/weixin_68840588/article/details/139728986)
   &gt;[备用链接](/assets/2024-09-11/simpread-Nginx%20&#43;%20KeepAlived%20高可用负载均衡集群_nginx&#43;keepalived-CSDN%20博客.html)

#### Windows
 - [PanguHA,一款Windows双机热备工具](https://zhuanlan.zhihu.com/p/658793111)
 - [PanguFlow](https://gitee.com/AaronE_2890620459/pangu-flow)
   &gt;PanguFlow是双机热备软件PanguHA的一个子模块，它是一款功能强大的文件实时同步软件，提供了文件热备的功能
 - [pangu-ha](https://gitee.com/AaronE_2890620459/pangu-ha)

&gt;个人狂想：目前Windows下，这个PanguHA工具是能够满足要求的，只是说需要付费购买授权，如果时间充足的话，可以参考Keepalived的实现，用asciinema/PowerSession-rs的思路来采用Rust自行实现一个Keepalived-rs，通过[windows-rs](https://github.com/microsoft/windows-rs)来调用Windows api

### 高质量实战
 - [分布式 --OpenResty&#43;lua&#43;Redis 实现限流与防爬虫](https://cloud.tencent.com/developer/article/2091388)
   &gt;[备用链接](/assets/2024-09-11/simpread-分布式%20--OpenResty&#43;lua&#43;Redis%20实现限流与防爬虫%20-%20腾讯云开发者社区%20-%20腾讯云.html)

## 参考资料
 - [OpenResty &amp; Nginx：详细对比与部署指南](https://blog.csdn.net/Hellc007/article/details/140143446)
 - [nginx官网](https://nginx.org/)
 - [Nginx 入门学习笔记](https://wangchujiang.com/nginx-tutorial/)
 - [Nginx 教程](https://dunwu.github.io/nginx-tutorial)
 - [《Nginx应用与运维实战》王小东](https://book.douban.com/subject/35174208/)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/nginx_notes/  

