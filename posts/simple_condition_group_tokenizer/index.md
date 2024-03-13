# 简易的条件组分词器


这是一个简单且易用的条件组分词器，提供了c#、c&#43;&#43;、python等多种语言的实现和示例，如果你的项目里需要支持多层条件或组合条件之类的机制，那么它将非常适合你

&lt;!--more--&gt;

{{&lt; link href=&#34;https://github.com/YaoXuanZhi/condition_group&#34; content=&#34;简易的条件组分词器&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

为了说明这个小玩具怎么用，这里以一个游戏项目为例

## 前言
在游戏项目的Gameplay开发中，条件配置是很大一项配置内容，而且经常变更，一个通用且可维护性强的条件支持机制就显得尤为重要了，这里提供了一种程序只需要为每类条件做机制支持，最终条件配置交由策划童鞋自行组合的配置方式，而且能够很好兼容excel、csv等配置

## 需求分析

策划童鞋设计一个功能开启需求，并且罗列了一些例子，如下所示：

|  系统功能名   |  开启条件   |
| :----: | :----------: |
| 武器系统  |   玩家等级达到5级    |
| 坐骑系统  |   玩家等级达到15级    |
| 天赋系统  |   武器系统升到10级【且】开启了坐骑系统    |
| 聊天系统  |   玩家等级达到30级【且】充值过    |
| 公会系统  |   玩家等级达到100级【或】累计登录30天    |

上述这些条件又应该是怎么配置到配置表上呢，比如在excel表上怎么配置这些条件呢

### 配置表设计
将罗列的开启条件进行整理，如下所示：

配置格式设计如下：`Type-Param1-Param2-Param3`

|  Type(条件类型)  |  Desc(描述)  |  Template(模板)  |  Example(示例)  |
| :----: | :----------: | :----------: | :----------: |
| is_system_open  |  系统A是否开启  |  is_system_open-system_id  | 武器系统是否开启:`is_system_open-weapon_sys` |
| system_level  |  系统A达到B等级  |   system_level-system_id-need_level    | 武器系统是否达到10级: `system_level-weapon_sys-10`
| player_level  | 玩家达到A等级   |  player_level-need_level  | 玩家是否达到10级：`player_level-10`  |
| acc_charge_total  | 累计充值总额达到A   |  acc_charge_total-need_total  | 是否累计充值总额已达1000元：`acc_charge_total-1000`  |
| acc_login_days  | 累计登录天数达到A   |  acc_login_days-need_days | 是否累计登录了30天：`acc_login_days-30`  |

各个具体条件已经拆分好了，那么怎么表述它的【且】和【或】关系呢，在此，引入了另外两个特殊符号，如下所示：

|  符号  |  作用  |
| :----: | :----------: |
| `&amp;&amp;` | 完成`条件A`和`条件B`的且运算 |
| `\|\|` | 完成`条件A`和`条件B`的或运算 |

那么再来到复杂的条件示例上，它们将会简化成如下配置：
 - 描述：`玩家等级达到30级【且】充值过` ==&gt; 配置项：`player_level-30 &amp;&amp; acc_charge_total-1`
 - 描述：`玩家等级达到100级【或】累计登录30天` ==&gt; 配置项：`player_level-100 || acc_login_days-30`

最终的配置表格式如下：

|  系统功能名   |  注释  |  开启条件   |
| :----: | :----------: | :----------: |
| 武器系统  |   玩家等级达到5级    |  `player_level-5`  |
| 坐骑系统  |   玩家等级达到15级    |  `player_level-15`  |
| 天赋系统  |   武器系统升到10级【且】开启了坐骑系统    |  `system_level-weapon_sys-10 &amp;&amp; is_system_open-mount_sys` |
| 聊天系统  |   玩家等级达到30级【且】充值过    |  `player_level-30 &amp;&amp; acc_charge_total-1`  |
| 公会系统  |   玩家等级达到100级【或】累计登录30天    |  `player_level-100 \|\| acc_login_days-30`  |

上述条件配置项已经很像我们编程语言里的条件表达式了，比如C&#43;&#43;里面的if statement，与之相比我们还差了一个`小括号()`的功能和`逻辑运算符:非`的支持，不过`非`一般用不上，可以酌情考虑要不要搞，有了`小括号()`的支持后，我们可以表达更加复杂的条件了，诸如：

 - 描述：`(武器系统升至50级 【且】锻造系统升至30级) 或 玩家充值达到10000元`
 - 配置项：`(system_level-weapon_sys-50 &amp;&amp; system_level-mount_sys-30) || acc_charge_total-10000`

给这类复杂条件起个名字，叫做`条件组`，因为每个小括号包裹住的条件可以当作一个条件分层组，如果完全没有小括号的话，可以看作它有个隐藏的小括号，同策划童鞋沟通好，达成共识

## 程序设计
上面已经将相关复杂条件描述的条件配置都设计好了，相关测试用例也收集了一些，那么接下来就是提供功能支持来解析条件配置，并且封装好判断接口，再将各个条件类型的支持给加上

程序实现步骤拆解：
  1. 实现一个Tokenizer：解析这个配置项文本，依据其设计好的分割规则，拆分成一个Token数组，然后提取里面的`条件块`和`逻辑运算符`和`层级关系`，举个简单例子：将 `system_level-weapon_sys-50 &amp;&amp; system_level-mount_sys-30` 解析成一个数组：`system_level-weapon_sys-50`、`&amp;&amp;`、`system_level-mount_sys-30`
  2. 将ConditionToken进行解析并返回结果：将上述例子里的 `system_level-weapon_sys-50`、`system_level-mount_sys-30` 进一步解析，并且调用各个系统的数据判断该条件的结果，将结果结合`&amp;&amp;`逻辑运算符得出结果，可以简化成 `result1 &amp;&amp; result2`

### 功能实现
参考[condition_group_tokenizer](https://github.com/YaoXuanZhi/condition_group) 里的Demo示例，重载`ProxyCondition()`方法，直接进入到步骤2即可

这里贴了Python版的测试用例，其它语言依葫芦画瓢就行

```python
# coding=utf-8
from condition_group_tokenizer import ConditionGroupTokenizer
import unittest

class PlayerFakeDataComponent:
    &#39;&#39;&#39;
    伪造玩家数据
    &#39;&#39;&#39;
    def __init__(self) -&gt; None:
        self.buildFakeDatas()

    def buildFakeDatas(self):
        &#39;&#39;&#39;
        测试用例可以重载该函数
        &#39;&#39;&#39;
        # 已开启的系统数据
        self.systemDatas = {
            &#34;weapon_sys&#34; : {&#34;level&#34;:39},
            &#34;mount_sys&#34; : {&#34;level&#34;:31},
            }

        # 累计登录天数
        self.accLoginDays = 3

        # 累计充值金额
        self.accChargeTotal = 3

        # 玩家等级
        self.level = 3

class PlayerConditionComponent(ConditionGroupTokenizer):
    &#34;&#34;&#34;
    玩家条件组件
    &#34;&#34;&#34;
    def __init__(self, playerDataComponent:PlayerFakeDataComponent) -&gt; None:
        super().__init__()
        self.playerDataComponent = playerDataComponent

    def proxyCondition(self, source:str, isPromt:bool) -&gt; bool:
        elements = source.split(&#34;-&#34;)

        atomType = elements[0]
        params = elements[1:]

        result = self.checkConditionAtom(atomType, params)
        if isPromt:
            print(f&#34;判断：{atomType}:{params} 结果为：{result}&#34;)
        return result

    def checkConditionAtom(self, atomType, params) -&gt; bool:
        &#34;&#34;&#34;
        根据条件类型来逐个判断
        &#34;&#34;&#34;

        if atomType == &#34;is_system_open&#34;:
            systemId = params[0]
            return systemId in self.playerDataComponent.systemDatas
        elif atomType == &#34;system_level&#34;:
            systemId = params[0]
            needSystemLevel = int(params[1])
            if systemId in self.playerDataComponent.systemDatas:
                systemInfo = self.playerDataComponent.systemDatas[systemId]
                return systemInfo[&#34;level&#34;] &gt;= needSystemLevel
            return False
        elif atomType == &#34;player_level&#34;:
            needPlayerLevel = int(params[0])
            return self.playerDataComponent.level &gt;= needPlayerLevel
        elif atomType == &#34;acc_charge_total&#34;:
            needAccTotal = int(params[0])
            return self.playerDataComponent.accChargeTotal &gt;= needAccTotal
        elif atomType == &#34;acc_login_days&#34;:
            needAccDays = int(params[0])
            return self.playerDataComponent.accLoginDays &gt;= needAccDays
        else: 
            assert False, f&#34;条件类型：{atomType} 还没支持，请完善相关条件判断逻辑&#34;
        return False

class TestCondtionGroupComponent(unittest.TestCase):
    def __init__(self, methodName: str = &#34;且运算逻辑&#34;) -&gt; None:
        super().__init__(methodName)
    def test_and_operator_success(self):
        &#39;&#39;&#39;
        测试用例-&amp;&amp;运算符:成功
        &#39;&#39;&#39;
        class TestDataComponent(PlayerFakeDataComponent):
            def buildFakeDatas(self):
                # 已开启的系统数据
                self.systemDatas = {
                    &#34;weapon_sys&#34; : {&#34;level&#34;:50},
                    &#34;mount_sys&#34; : {&#34;level&#34;:60},
                    }

                # 累计登录天数
                self.accLoginDays = 3

                # 累计充值金额
                self.accChargeTotal = 3

                # 玩家等级
                self.level = 3

        source = &#34;system_level-weapon_sys-50 &amp;&amp; system_level-mount_sys-30&#34;
        dataComponent = TestDataComponent()
        conditionComponent = PlayerConditionComponent(dataComponent)
        result = conditionComponent.directCheck(source, False)
        self.assertTrue(result, f&#34;判断：{source} 结果为：{result}&#34;)

    def test_and_operator_fail(self):
        &#39;&#39;&#39;
        测试用例-&amp;&amp;运算符:失败
        &#39;&#39;&#39;
        class TestDataComponent(PlayerFakeDataComponent):
            def buildFakeDatas(self):
                &#39;&#39;&#39;
                伪造玩家数据
                &#39;&#39;&#39;
                # 已开启的系统数据
                self.systemDatas = {
                    &#34;weapon_sys&#34; : {&#34;level&#34;:50},
                    &#34;mount_sys&#34; : {&#34;level&#34;:29},
                    }

                # 累计登录天数
                self.accLoginDays = 3

                # 累计充值金额
                self.accChargeTotal = 3

                # 玩家等级
                self.level = 3

        source = &#34;system_level-weapon_sys-50 &amp;&amp; system_level-mount_sys-30&#34;
        dataComponent = TestDataComponent()
        conditionComponent = PlayerConditionComponent(dataComponent)
        result = conditionComponent.directCheck(source, False)
        self.assertFalse(result, f&#34;判断：{source} 结果为：{result}&#34;)

    def test_or_operator_success(self):
        &#39;&#39;&#39;
        测试用例-&amp;&amp;运算符:成功
        &#39;&#39;&#39;
        class TestDataComponent(PlayerFakeDataComponent):
            def buildFakeDatas(self):
                &#39;&#39;&#39;
                伪造玩家数据
                &#39;&#39;&#39;
                # 已开启的系统数据
                self.systemDatas = {
                    &#34;weapon_sys&#34; : {&#34;level&#34;:50},
                    &#34;mount_sys&#34; : {&#34;level&#34;:29},
                    }

                # 累计登录天数
                self.accLoginDays = 12

                # 累计充值金额
                self.accChargeTotal = 3

                # 玩家等级
                self.level = 3

        source = &#34;is_system_open-pet_system || (acc_charge_total-100 || acc_login_days-10) || player_level-5&#34;
        dataComponent = TestDataComponent()
        conditionComponent = PlayerConditionComponent(dataComponent)
        result = conditionComponent.directCheck(source, False)
        self.assertTrue(result, f&#34;判断：{source} 结果为：{result}&#34;)

    def test_or_operator_fail(self):
        &#39;&#39;&#39;
        测试用例-&amp;&amp;运算符:成功
        &#39;&#39;&#39;
        class TestDataComponent(PlayerFakeDataComponent):
            def buildFakeDatas(self):
                &#39;&#39;&#39;
                伪造玩家数据
                &#39;&#39;&#39;
                # 已开启的系统数据
                self.systemDatas = {
                    &#34;weapon_sys&#34; : {&#34;level&#34;:50},
                    &#34;mount_sys&#34; : {&#34;level&#34;:29},
                    }

                # 累计登录天数
                self.accLoginDays = 3

                # 累计充值金额
                self.accChargeTotal = 3

                # 玩家等级
                self.level = 3

        source = &#34;is_system_open-pet_system || (acc_charge_total-100 || acc_login_days-10) || player_level-5&#34;
        dataComponent = TestDataComponent()
        conditionComponent = PlayerConditionComponent(dataComponent)
        result = conditionComponent.directCheck(source, False)
        self.assertFalse(result, f&#34;判断：{source} 结果为：{result}&#34;)

if __name__ == &#39;__main__&#39;:
    unittest.main()
```

## 结尾
在游戏项目里，我们仅需实现一次通用条件组件模块，然后在奖励发放、任务触发&amp;完成、成就事件达成等业务模块上，都调用这个通用组件来判断条件是否满足

当然，真正要集成到项目里，还需要考虑到非法输入等情况，配置检查之类的事情，这块没法偷懒

### 参考资料
 - [游戏内条件集合(与或非组合)的判定处理 C#版](https://zhuanlan.zhihu.com/p/98064735)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://yaoxuanzhi.github.io/posts/simple_condition_group_tokenizer/  

