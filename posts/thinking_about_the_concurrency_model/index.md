# 关于并发模型的思考


当摩尔定律的影响力逐渐消退，我们有必要回顾那段硬件资源受限、成本高昂的岁月。在那个时代，开发者们不得不发挥创造力，深入挖掘机器的潜力，精心优化任务调度，从而孕育出一系列高效的并发处理策略，这里，主要是探讨下主流的Actor模型和CSP模型

&lt;!--more--&gt;

{{&lt; link href=&#34;http://www.jdon.com/concurrent/actor-csp.html&#34; content=&#34;核心内容是来自《Actor模型和CSP模型的区别》&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

### Actor模型
在Actor模型中，主角是Actor，类似一种worker，Actor彼此之间直接发送消息，不需要经过什么中介，消息是异步发送和处理的：

  &lt;div align=&#34;center&#34;&gt;
    &lt;img src=&#34;/assets/2021-04-28/actors.png&#34;&gt;
  &lt;/div&gt;

Actor模型描述了一组为了避免并发编程的常见问题的公理:

  - 所有Actor状态是Actor本地的，外部无法访问。
  - Actor必须只有通过消息传递进行通信。  
  - 一个Actor可以响应消息:推出新Actor，改变其内部状态，或将消息发送到一个或多个其他参与者。
  - Actor可能会堵塞自己，但Actor不应该堵塞它运行的线程。

更多可见[Actor模型专题](http://www.jdon.com/actors.html)

### Channel模型
Channel模型中，worker之间不直接彼此联系，而是通过不同channel进行消息发布和侦听。消息的发送者和接收者之间通过Channel松耦合，发送者不知道自己消息被哪个接收者消费了，接收者也不知道是哪个发送者发送的消息。

Go语言的CSP模型是由协程Goroutine与通道Channel实现：

  &lt;div align=&#34;center&#34;&gt;
    &lt;img src=&#34;/assets/2021-04-28/channel.png&#34;&gt;
  &lt;/div&gt;

  - Go协程goroutine
    &gt;是一种轻量线程，它不是操作系统的线程，而是将一个操作系统线程分段使用，通过调度器实现协作式调度。是一种绿色线程，微线程，它与Coroutine协程也有区别，能够在发现堵塞后启动新的微线程。
  - 通道channel
    &gt;类似Unix的Pipe，用于协程之间通讯和同步。协程之间虽然解耦，但是它们和Channel有着耦合。

### Actor模型和CSP区别
Actor模型和CSP区别图如下：

  &lt;div align=&#34;center&#34;&gt;
    &lt;img src=&#34;/assets/2021-04-28/actor_csp.png&#34;&gt;
  &lt;/div&gt;

Actor之间直接通讯，而CSP是通过Channel通讯，在耦合度上两者是有区别的，后者更加松耦合。

同时，它们都是描述独立的流程通过消息传递进行通信。主要的区别在于：`在CSP消息交换是同步的(即两个流程的执行&#34;接触点&#34;的，在此他们交换消息)，而Actor模型是完全解耦的，可以在任意的时间将消息发送给任何未经证实的接受者`。由于Actor享有更大的相互独立，因为它可以根据自己的状态选择处理哪个传入消息，自主性更大些。

在Go语言中为了不堵塞流程，程序员必须检查不同的传入消息，以便预见确保正确的顺序。CSP好处是Channel不需要缓冲消息，而Actor理论上需要一个无限大小的邮箱作为消息缓冲。

### 发展现状
  - Actor模型最初由Erlang推出，后已被多种编程语言实现了，其中c语言里面，出名的有云风大大维护的`skynet`，采用c/lua混合开发，而c&#43;&#43;有caf，java有akka等等
  - CSP模型也是如此，目前在Golang这里最为出名

现如今，在游戏服务端开发领域里，Actor模型在c/c&#43;&#43;/lua、erlang这几块发展得红红火火，而CSP模型则以Golang技术栈马首是瞻

### 参考资料
 - [《Actor模型和CSP模型的区别》](http://www.jdon.com/concurrent/actor-csp.html)
 - [《为什么Actor模型是高并发事务的终极解决方案？》](http://www.jdon.com/45728)
 - [《Actors编程模型》](https://www.jdon.com/actors.html)
 - [《The Actor Model (everything you wanted to know...)》](https://v.qq.com/x/page/c032075lfoq.html)
 - [《ActorLite: 一个轻量级Actor模型实现》](http://www.cnblogs.com/and_swordday/p/4113623.html)
 - [《Comparison between 4 actor frameworks》](http://doc.akka.io/docs/misc/Comparison_between_4_actor_frameworks.pdf)
 - [《一个轻量级Actor并发框架的c&#43;&#43;实现， libgsc(Game Server Communication Library)》](http://blog.csdn.net/xzwdev/article/details/40791387)
 - [《详解Theron通过Actor模型解决C&#43;&#43;并发编程的一种思维》](http://www.shenyongxiang.com/content-15232-1/)
 - [The C&#43;&#43; Actor Framework官网](https://www.actor-framework.org/)

---

#### 吐槽
近十多年来，在摩尔定律大抵还生效的时期里，App开发采取的技术方案越来越耗机器资源了，达到了一种不可理喻的程度，比如有些App开发者采用了各种Web App开发策略、桌面软件开发者采用了类似Electron框架来进行客户端开发等，导致网上许多功能简单的程序，安装包体积巨大，这实际是对机器资源的一种巨大浪费，怀念以前的小巧精致的App

很多方案是跨平台需求带进来的妥协，好在，有开发者顶不住Electron巨大的分发体积，搞出了tauri方案，而在原生跨平台开发领域里，Google推出了Flutter，发展势头也非常好

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://example.org/posts/thinking_about_the_concurrency_model/  

