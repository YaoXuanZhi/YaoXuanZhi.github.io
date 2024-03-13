# C&#43;&#43; 代码规范


{{&lt; link href=&#34;https://zh-google-styleguide.readthedocs.io&#34; content=&#34;Google 开源项目风格指南&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

&lt;!--more--&gt;

## 前言
在c&#43;&#43;这个大联盟里，有许许多多领导组织，微软、Google、IBM，还有国内各个大厂内部因地制宜定下的各种代码规范，而在我目前的职业生涯里，先后经历了两种c&#43;&#43;代码规范洗礼，其实项目内部具体用什么代码规范，一般都由leader定下的，新加入团队的成员理应遵循已有的代码规范，保证团队工程风格的一致性

但是，作为开发大军里的一员，除开企业内的团队开发，我们私下也会有自己的创造欲望，去捣弄自己的小项目，那么在这个时候，就需要定下一个从一而终的代码规范了，如果机遇一到，拉到融资或者和其它小伙伴一起创业，它就能作为团队规范的一部分了

目前在业界之内，谷歌的开源氛围是较为浓厚的，它提供了许多深受业界认可的开源项目，比如chromium、grpc、protobuf、gson……，还往业界输出了它的[《Google 开源项目风格指南》](https://zh-google-styleguide.readthedocs.io)，而之前自己在c&#43;&#43;学习的过程中，也用到了其中一些开源库

而刚毕业那会，由于做的是windows下的win32软件开发，因此最开始用的是mfc那套规范；后续进了游戏行业，一直从事游戏服务端开发，此时起就采用google的代码规范了。现今反思了一些东西，自己要想长远发展的话，采用谷歌这套会更好些

---

## 命名规范
### 通用命名规则
- 函数命名，变量命名，文件命名要有描述性；少用缩写。
- 尽可能给有描述性的命名，别心疼空间，毕竟让代码易于新读者理解很重要。不要用只有项目开发者能理解的缩写，也不要通过砍掉几个字母来缩写单词。
```c&#43;&#43;
    //示例：
    int price_count_reader;    // 无缩写
    int num_errors;            // “num” 本来就很常见
    int num_dns_connections;   // 人人都知道 “DNS” 是啥

    //反例：
    int n;                     // 莫名其妙。
    int nerr;                  // 怪缩写。
    int n_comp_conns;          // 怪缩写。
    int wgc_connections;       // 只有贵团队知道是啥意思。
    int pc_reader;             // &#34;pc&#34; 有太多可能的解释了。
    int cstmr_id;              // 有删减若干字母。
```

### 文件命名
- 文件名要全部小写, 可以包含下划线 `(_)` 或连字符 `(-)`. 按项目约定来. 如果并没有项目约定，`_` 更好。接受的文件命名:
    * my_useful_class.cc
    * my-useful-class.cc
    * myusefulclass.cc
    * muusefulclass_test.cc // ``_unittest`` 和 ``_regtest`` 已弃用。

- C&#43;&#43; 文件要以 **.cc** 结尾, 头文件以 **.h** 结尾. 专门插入文本的文件则以 **.inc** 结尾，参见:``ref:self-contained headers``。
- 不要使用已经存在于 `/usr/include` 下的文件名 (Yang.Y 注: 即编译器搜索系统头文件的路径), 如`db.h`.
- 通常应尽量让文件名更加明确. `http_server_logs.h` 就比 `logs.h` 要好. 定义类时文件名一般成对出现, 如 `foo_bar.h` 和 `foo_bar.cc`, 对应于类 `FooBar`.
- 内联函数必须放在 `.h` 文件中. 如果内联函数比较短, 就直接放在 `.h` 中.

### 类型命名
- 类型名称的每个单词首字母均大写, 不包含下划线: `MyExcitingClass`, `MyExcitingEnum`.
- 所有类型命名 —— 类, 结构体, 类型定义 (typedef), 枚举 —— 均使用相同约定. 例如:
```c&#43;&#43;
    // classes and structs
    class UrlTable { ...
    class UrlTableTester { ...
    struct UrlTableProperties { ...

    // typedefs
    typedef hash_map&lt;UrlTableProperties *, string&gt; PropertiesMap;

    // enums
    enum UrlTableErrors { ...
```

### 变量命名
- 变量名一律小写, 单词之间用下划线连接. 类的成员变量以下划线结尾, 但结构体的就不用，如`::a_local_variable`, `a_struct_data_member`, `a_class_data_member_`.

1. 【普通变量命名】:
```c&#43;&#43;
    //举例:
    string table_name;  // 可 - 用下划线。
    string tablename;   // 可 - 全小写。

    //反例:
    string tableName;   // 差 - 混合大小写。
```

2. 【类数据成员】：
不管是静态的还是非静态的，结构体数据成员都可以和普通变量一样, **但要接下划线**。
```c&#43;&#43;
    class TableInfo 
    {
    private:
        string table_name_;             // 可 - 尾后加下划线。
        string tablename_;              // 可。
        static Pool&lt;TableInfo&gt;* pool_;  // 可。
    };
```

3. 【结构体变量】:
不管是静态的还是非静态的，结构体数据成员都可以和普通变量一样, **不用像类那样接下划线**:
```c&#43;&#43;
    struct UrlTableProperties {
        string name;
        int num_entries;
    }
```

4. 【全局变量】:
对全局变量没有特别要求, 少用就好, 但如果你要用, 可以用 ``g_`` 或其它标志作为前缀, 以便更好的区分局部变量.

5. 【常量命名】
在全局或类里的常量名称前加 ``k``: `kDaysInAWeek`. 且除去开头的 ``k``之外每个单词开头字母均大写。
所有编译时常量, 无论是局部的, 全局的还是类中的, 和其他变量稍微区别一下. 这规则适用于编译时的局部作用域常量，不过要按变量规则来命名也可以。
    ``k``后接大写字母开头的单词:
```c&#43;&#43;
    const int kDaysInAWeek = 7;
```

6. 【函数命名】
常规函数使用大小写混合, 取值和设值函数则要求与变量名匹配: 
```c&#43;&#43;
    MyExcitingFunction(),
    MyExcitingMethod(), 
    my_exciting_member_variable(), 
    set_my_exciting_member_variable(),
    自定义函数不能与系统或库函数同名.
```

* [常规函数]:
函数名的**每个单词首字母大写**, **没有下划线**。
如果您的某函数出错时就要直接 `crash`, 那么就在函数名加上 `OrDie`. 但这函数本身必须集成在产品代码里，且平时也可能会出错。
```c&#43;&#43;
    AddTableEntry()
    DeleteUrl()
    OpenFileOrDie()
```

* [取值和设值函数]:
取值（**Accessors**）和设值（**Mutators**）函数要与存取的变量名匹配. 这儿摘录一个类,`num_entries_` 是该类的实例变量:
```c&#43;&#43;
    class MyClass {
    public:
        ...
        int GetNumEntries() const { return num_entries_; }
        void SetNumEntries (int num_entries) { num_entries_ = num_entries; }

    private:
        int num_entries_;
    };
```

* 其它非常短小的内联函数名也可以用小写字母, 例如. 如果你在循环中调用这样的函数甚至都不用缓存其返回值, 小写命名就可以接受.

7. 【名字空间命名】
* 名字空间用小写字母命名.

8. 【枚举命名】
- 枚举的命名应当和``常量``或``宏``一致: kEnumName 或是 ENUM_NAME.
- 单独的枚举值应该优先采用``常量``的命名方式. 但``宏``方式的命名也可以接受. 
- 枚举名 UrlTableErrors(以及 AlternateUrlTableErrors) 是类型, 所以要用大小写混合的方式.
```c&#43;&#43;
    enum UrlTableErrors {
        kOK = 0,
        kErrorOutOfMemory,
        kErrorMalformedInput,
    };
    enum AlternateUrlTableErrors {
        OK = 0,
        OUT_OF_MEMORY = 1,
        MALFORMED_INPUT = 2,
    };
```

9. 【宏命名】
你并不打算使用宏 ``&lt;preprocessor-macros&gt;``, 对吧? 如果你一定要用, 像这样命名:``MY_MACRO_THAT_SCARES_SMALL_CHILDREN``.
通常``不应该``使用宏. 如果不得不用, 其命名像枚举命名一样全部大写, 使用下划线:
```c&#43;&#43;
    #define ROUND(x) ...
    #define PI_ROUNDED 3.0
```

10. 【命名规则的特例】
[X]如果你命名的实体与已有 C/C&#43;&#43; 实体相似, 可参考现有命名策略.


## 注释规范
这块倾向使用doxgen提供的注释规范，因为如果后续需要自动生成一份开发文档，这个就比较方便

不过，注释固然很重要, 但好的代码本身应该是自文档化的，有意义的类型名和变量名, 要远胜过要用注释解释的含糊不清的名字

1. 【注释风格】
    * 使用 `//` 或 `/* */`, 统一就好
    * `//` 或 `/* */` 都可以; 但 `//` 更常用些
    * 要在如何注释及注释风格上确保统一

2. 【文件注释】
    * .h 文件要对所声明的类的功能和用法作简单说明，如果可以的话，可以在文件头上罗列一份`feature_list`或者`todo_list`
    * .cc 文件通常包含了更多的实现细节或算法技巧讨论
    * 如果你感觉这些实现细节或算法技巧讨论对于理解`.h`文件有帮助, 可以该注释挪到`.h`, 并在`.cc`中指出文档在`.h`中
    * 不要简单的在`.h`和`.cc`间复制注释. 这种偏离了注释的实际意义

    文件头举例如下:

    ```c&#43;&#43;
    /*****************************************************************************
    *  @file     auto_register_factory.h
    *  @brief    可注册的工厂类模板
    *  @note
    *  C&#43;&#43;反射机制的一种实现，基于类行为抽象而成
    *****************************************************************************/
    ```

3. 【类注释】
    每个类的定义都要附带一份注释, 描述类的功能和用法.

    举例如下:
    ```c&#43;&#43;
    /**
    * @class CSigleRegisterFactory
    * @brief 一个支持自动注册的工厂类模板
    * @note
    * 注意，如果TKey是自定义数据类型，那么请重载其关系运算符哦，
    * 具体看std::map的条件，通常仅需要重载运算符“&lt;”，详情请看
    * https://msdn.microsoft.com/ZH-CN/library/s44w4h2s(v=VS.100,d=hv.2).aspx
    * 在gcc下编译可能遇到的问题：http://blog.csdn.net/pb1995/article/details/49532285
    * 仅仅支持一对一的模式
    */
    template&lt;typename TKey, typename TBase&gt;
    class CSingleRegisteredFactory {
    ...
    };

    ```

4. 【函数注释】
    函数声明处注释描述函数功能，定义处描述函数实现

    [函数声明]:
    注释位于声明之前, 对函数功能及用法进行描述. 注释使用叙述式 (“Opens the file”) 而非指令式 (“Open the file”); 注释只是为了描述函数, 而不是命令函数做什么. 通常, 注释不会描述函数如何工作. 那是函数定义部分的事情

    函数声明处注释的内容:
    函数的输入输出.
    对类成员函数而言: 函数调用期间对象是否需要保持引用参数, 是否会释放这些参数.
    如果函数分配了空间, 需要由调用者释放.
    参数是否可以为 NULL.
    是否存在函数使用上的性能隐患.
    如果函数是可重入的, 其同步前提是什么?
    使用brief、param、return、note等来描述具体的注释块

    举例如下:

    ```c&#43;&#43;
    /**
    * @brief: 将某类注册到工厂类实例之中
    * @param[in] TKey  Key  某类在外部检索时所用到的条件，通常采用字符串或枚举等类型
    * @param[in|out] IForwarder&lt;TBase&gt;*  pobj  待注册到工厂类实例的基类指针，自动跳过空指针
    * @param[in] bool  bIsOverWrite  如果为true，则重新注册此类，否则，直接释放待注册的基类指针
    * @return 无
    * @note
    * 本类会自动管理所有想要注册到本类工厂的基类指针，因此需要对重复注册的类指针自动进行释放
    */
    void RegisterFactory(TKey key, ImpForwarder *ptr_obj, bool is_overWrite = false)
    ```

    但也要避免啰啰嗦嗦, 或做些显而易见的说明. 下面的注释就没有必要加上 “returns false otherwise”, 因为已经暗含其中了:

    ```c&#43;&#43;
    // Returns true if the table cannot hold any more entries.
    bool IsTableFull();
    ```

    注释构造/析构函数时, 切记读代码的人知道构造/析构函数是干啥的, 所以 “destroys this object” 这样的注释是没有意义的. 注明构造函数对参数做了什么 (例如, 是否取得指针所有权) 以及析构函数清理了什么. 如果都是些无关紧要的内容, 直接省掉注释. 析构函数前没有注释是很正常的.

    [函数定义]:
    每个函数定义时要用注释说明函数功能和实现要点. 比如说说你用的编程技巧, 实现的大致步骤, 或解释如此实现的理由, 为什么前半部分要加锁而后半部分不需要.
    不要 从 .h 文件或其他地方的函数声明处直接复制注释. 简要重述函数功能是可以的, 但注释重点要放在如何实现上.

5. 【变量注释】
    通常变量名本身足以很好说明变量用途. 某些情况下, 也需要额外的注释说明.

    [类数据成员]:
    每个类数据成员 (也叫实例变量或成员变量) 都应该用注释说明用途. 如果变量可以接受 NULL 或 -1 等警戒值, 须加以说明. 比如:

    ```c&#43;&#43;
    private:
    // Keeps track of the total number of entries in the table.
    // Used to ensure we do not go over the limit. -1 means
    // that we don&#39;t yet know how many entries the table has.
    int num_total_entries_;
    ```

    [全局变量]:
    和数据成员一样, 所有全局变量也要注释说明含义及用途. 比如:
    ```
    // The total number of tests cases that we run through in this regression test.
    const int kNumTestCases = 6;
    ```

6. 【实现注释】
    对于代码中巧妙的, 晦涩的, 有趣的, 重要的地方加以注释.
    [代码前注释]:
    巧妙或复杂的代码段前要加注释. 比如:

    ```c&#43;&#43;
    // Divide result by two, taking into account that x
    // contains the carry from the add.
    for (int i = 0; i &lt; result-&gt;size(); i&#43;&#43;) {
        x = (x &lt;&lt; 8) &#43; (*result)[i];
        (*result)[i] = x &gt;&gt; 1;
        x &amp;= 1;
    }
    ```

    [行注释]:
    比较隐晦的地方要在行尾加入注释. 在行尾空两格进行注释. 比如:

    ```c&#43;&#43;
    // If we have enough memory, mmap the data portion too.
    mmap_budget = max&lt;int64&gt;(0, mmap_budget - index_-&gt;length());
    if (mmap_budget &gt;= data_size_ &amp;&amp; !MmapData(mmap_chunk_bytes, mlock))
        return;  // Error already logged.
    ```
    注意, 这里用了两段注释分别描述这段代码的作用, 和提示函数返回时错误已经被记入日志.

    如果你需要连续进行多行注释, 可以使之对齐获得更好的可读性:

    ```c&#43;&#43;
    DoSomething();                  	// Comment here so the comments line up.
    DoSomethingElseThatIsLonger();  	// Comment here so there are two spaces between
                                    // the code and the comment.
    { // One space before comment when opening a new scope is allowed,
      // thus the comment lines up with the following comments and code.
      DoSomethingElse();  // Two spaces before line comments normally.
    }
    NULL, true/false, 1, 2, 3...:
    ```

    向函数传入 NULL, 布尔值或整数时, 要注释说明含义, 或使用常量让代码望文知意. 例如, 对比:

    ```c&#43;&#43;
    bool success = CalculateSomething(interesting_value,
                                  10,
                                  false,
                                  NULL);  // What are these arguments??
    ```
    和:
    ```c&#43;&#43;
    bool success = CalculateSomething(interesting_value,
                                      10,     	// Default base value.
                                      false,  	// Not the first time we&#39;re calling this.
                                      NULL);  	// No callback.
    ```

    或使用常量或描述性变量:
    ```c&#43;&#43;
    const int kDefaultBaseValue = 10;
    const bool kFirstTimeCalling = false;
    Callback *null_callback = NULL;
    bool success = CalculateSomething(interesting_value,
                                      kDefaultBaseValue,
                                      kFirstTimeCalling,
                                      null_callback);
    ```

    不允许:
    注意 永远不要 用自然语言翻译代码作为注释. 要假设读代码的人 C&#43;&#43; 水平比你高, 即便他/她可能不知道你的用意:
    ```c&#43;&#43;
    // 现在, 检查 b 数组并确保 i 是否存在,
    // 下一个元素是 i&#43;1.
    ...        // 天哪. 令人崩溃的注释.
    ```

7. 【标点, 拼写和语法】
    注意标点, 拼写和语法; 写的好的注释比差的要易读的多.
    注释的通常写法是包含正确大小写和结尾句号的完整语句. 短一点的注释 (如代码行尾注释) 可以随意点, 依然要注意风格的一致性. 完整的语句可读性更好, 也可以说明该注释是完整的, 而不是一些不成熟的想法.

    虽然被别人指出该用分号时却用了逗号多少有些尴尬, 但清晰易读的代码还是很重要的. 正确的标点, 拼写和语法对此会有所帮助.

8. 【TODO 注释】
    对那些临时的, 短期的解决方案, 或已经够好但仍不完美的代码使用 TODO 注释.
    TODO 注释要使用全大写的字符串 TODO, 在随后的圆括号里写上你的大名, 邮件地址, 或其它身份标识. 冒号是可选的. 主要目的是让添加注释的人 (也是可以请求提供更多细节的人) 可根据规范的 TODO 格式进行查找. 添加 TODO 注释并不意味着你要自己来修正.
    ```c&#43;&#43;
    // TODO(kl@gmail.com): Use a &#34;*&#34; here for concatenation operator.
    // TODO(Zeke) change this to use relations.
    如果加 TODO 是为了在 “将来某一天做某事”, 可以附上一个非常明确的时间 “Fix by November 2005”), 或者一个明确的事项 (“Remove this code when all clients can handle XML responses.”).
    ```

9. 【弃用注释】
    通过弃用注释（DEPRECATED comments）以标记某接口点（interface points）已弃用。
    您可以写上包含全大写的 DEPRECATED 的注释，以标记某接口为弃用状态。注释可以放在接口声明前，或者同一行。

    在 DEPRECATED 一词后，留下您的名字，邮箱地址以及括号补充。

    仅仅标记接口为 DEPRECATED 并不会让大家不约而同地弃用，您还得亲自主动修正调用点（callsites），或是找个帮手。

    修正好的代码应该不会再涉及弃用接口点了，着实改用新接口点。如果您不知从何下手，可以找标记弃用注释的当事人一起商量。

10. 【其它】
    注释要言简意赅, 不要拖沓冗余, 复杂的东西简单化和简单的东西复杂化都是要被鄙视的;
    对于 Chinese coders 来说, 用英文注释还是用中文注释, `it is a problem`, 但不管怎样, 注释是为了让别人看懂, 难道是为了炫耀编程语言之外的你的母语或外语水平吗；
    注释不要太乱, 适当的缩进才会让人乐意看. 但也没有必要规定注释从第几列开始 (我自己写代码的时候总喜欢这样), UNIX/LINUX 下还可以约定是使用 tab 还是 space, 个人倾向于 space，并且是`4个space`;
    TODO 很不错, 有时候, 注释确实是为了标记一些未完成的或完成的不尽如人意的地方, 这样一搜索, 就知道还有哪些活要干, 日志都省了；另外，如果发现了一个bug，可以给它加个FIXME

    [代码段分隔]
    一个简单的函数里，其代码也是可以区分出不同的职能，在每部分职能切换的时候，加上换行

---
![Google C&#43;&#43; Style Guide](/assets/2021-04-27/google_cplusplus_style_guide.png)

---
## 参考资料
 - [Google 开源项目风格指南 - C&#43;&#43;风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/)
 - [Google C&#43;&#43; Style Guide](https://google.github.io/styleguide/cppguide.html)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://example.org/posts/cplusplus_style_guide/  

