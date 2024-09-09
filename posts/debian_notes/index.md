# Debian的折腾笔记


ubuntu镜像相对较大，并且UI风格相对老旧，后面遇到了Debian，看中它的全社区维护、永久开源的承诺和更现代的UI风格，因此入了坑，在此爬坑留痕

&lt;!--more--&gt;

---

## 账户权限设置
创建非root用户，并且设置ALL权限，否则不能使用sudo命令，想创建文件夹、安装docker等行为就无从谈起

```sh
su root
sudo adduser brew
sudo vim /etc/sudoers
```

添加上这行

```text
brew ALL=(ALL:ALL) ALL
```

## docker相关
解决了国内docker被禁止之后，Linux下的docker安装部署问题，详情看这里：[解决Docker国内网络问题](https://github.com/tech-shrimp/docker_installer)

### 安装docker
```sh
sudo curl -fsSL https://gitee.com/tech-shrimp/docker_installer/releases/download/latest/linux.sh| bash -s docker --mirror Aliyun
```

```sh
# 参考自：https://www.runoob.com/docker/docker-compose.html
sudo curl -L &#34;https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)&#34; -o /usr/local/bin/docker-compose

sudo chmod &#43;x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 验证docker-compose命令是否安装成功
docker-compose version
```

### 镜像操作
```sh
# 查看已pull的镜像
sudo docker images

# 移除镜像
sudo docker images remove mzz2017/v2raya


# 将已经pull下来的镜像打包成tar
sudo docker save -o v2raya.tar mzz2017/v2raya

# 加载离线镜像
sudo docker load -i v2raya.tar
```

### 容器实例操作
```sh
# 查看docker实例运行情况
sudo docker ps -a

# 进入容器实例内部的bash
sudo docker exec -it &lt;container_id&gt; /bin/bash

# 运行容器实例
sudo docker start &lt;container_id&gt;

# 停止容器实例
sudo docker stop &lt;container_id&gt;

# 移除容器实例
sudo docker remove &lt;container_id&gt;
```

## 安装npm和yarn
```sh
sudo apt install npm
# 如果apt/yum安装的node版本太低，可以用以下方式
## 一键安装node脚本
# curl -sL https://deb.nodesource.com/setup_20.x
## 使用nvm来管理多个Node版本
# curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
# cd $HOME/.nvm
# nvm ls-remote
# nvm install 22

sudo npm install -g yarn --registry=https://registry.npmmirror.com
```

## 禅道部署
```sh
#  - [docker安装zentao](https://www.zentao.net/book/zentaopms/docker-1111.html)
sudo mkdir ~/Software
sudo mkdir ~/Software/zentao_data
sudo docker run -d -v ~/Software/zentao_data:/data -p 90:80 -e MYSQL_INTERNAL=true hub.zentao.net/app/zentao:20.6
```

## 网络安全注意事项
 - [MongoDB的默认策略导致的安全问题](https://help.aliyun.com/zh/cloud-firewall/use-cases/best-practices-to-defend-against-unauthorized-access-to-a-mongodb-database)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/debian_notes/  

