# 排行榜实现的二三事


在我们生活中有各种各样排行榜，比如电商、社交网站上的各种热度榜、周榜、月榜等等，而在游戏里，这类排行榜也是比较常见，比如战力榜、积分榜、鲜花榜啥的，下面谈谈它们在不同游戏服务器架构下的实现

&lt;!--more--&gt;

---

### 全区全服架构
各种社交类游戏，比如房间类PVP，Moba、棋牌、赛车类项目，这种比较常见，这种游戏一般都有一个登录大厅，然后匹配进到同一个房间PK，服务器的压力主要在于匹配大厅的承载量，以及全服排行榜、全服聊天等等公共服务上

这类游戏一般会用到Redis中间件，可以直接基于Redis的有序集合zset来实现通用排行榜，这样一来该服务还拥有了备份、容灾及高可用等能力；不过如果项目没有使用到redis，那么自己手撸一个跳跃表(skip list)也可以做到，zset的底层实现是跳跃表，但是这样高可用以及容灾能力都得自己另外支持了，个人并不推荐

##### 技术Demo
基于以上思路，本人实现了一个简单的技术 Demo，如下图所示：

![基于flask实现的全区全服排行榜Demo](/assets/2020-05-19/17898798.png)

**开启负载均衡**

运行 `global_rank_server\deps\openresty-1.27.1.1-win64\nginx.exe` 则可以开启负载均衡，访问：[http://127.0.0.1:8020/](http://127.0.0.1:8020/) 即可看到效果

![负载均衡后的效果](/assets/2020-05-19/17898710.png)

&gt;附件 [global_rank_server.zip](/assets/2020-05-19/global_rank_server.zip)

### 分区分服架构
一般来说，分区分服多出现在各种MMO游戏项目，比如MMO 回合制、MMO Arpg、MMO SLG之类的，这类单服玩家数相对较少，因此即便排行榜采用全排序也能满足需求。然而，对于跨服排行榜，考虑到它的玩家规模不定，通常是高于单服至少一个数量级，建议借鉴全区全服的排行榜解决思路，如果项目里没有引入Redis，那就基于跳跃表实现一个高效通用的排行榜机制，这样可以确保排行榜的响应速度和数据处理能力，满足大规模玩家的需求

### 参考链接
 - [游戏全区全服和分区分服 QQ斗地主的设计](https://www.cnblogs.com/MYSQLZOUQI/p/8149080.html)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/global_rank_server_imp/  

