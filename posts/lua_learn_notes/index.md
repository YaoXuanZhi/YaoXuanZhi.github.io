# Lua学习笔记


{{&lt; link href=&#34;https://www.lua.org/&#34; content=&#34;Lua官网&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

&lt;!--more--&gt;

---

### 在Lua实现带默认参数的函数
在lua之中，它没有提供像C&#43;&#43;那样的不同参数列表的同名函数是不同的机制，在它的内部里面，其实现了一种很朴素的做法

假设定义了一个有n个形参的函数 `SetArgs(arg1...argn)`，那么若果开发者在实际调用此函数的时候，填充的参数的个数小于n，那么后面没有添加的参数的实参都默认被设为nil，此时通过or运算符来设定默认值即可；另外，也并不支持 `SetArgs(arg1 = 1,arg2 = 2, argn = n )`之类的语句，如下所示：

```lua
myTable = { a= 1, b=2, c=3, d=4 } 

--一种难以言喻的操作
function myTable:SetParam(a1,b1,c1,d1)
    self.a = a1 or &#34;a1&#34;
    self.b = b1 or &#34;b1&#34;
    self.c = c1 or &#34;c1&#34;
    self.d = d1 or &#34;d1&#34;
end

--并没有这种骚操作
--function myTable:SetParam(a1 = &#34;a1&#34;,b1 = &#34;b1&#34;,c1 = &#34;c1&#34;,d1 = &#34;c1&#34;)
--    self.a = a1
--    self.b = b1
--    self.c = c1
--    self.d = d1
--end

local temp = myTable
temp:SetParam(&#34;Adobe&#34;)
print(temp.a)
print(temp.b)
print(temp.c)
print(temp.d)

print(&#34;end&#34;)
```

### 模拟C&#43;&#43;的类的继承机制

更多相关资料请看[Metatable In Lua 浅尝辄止](http://www.cnblogs.com/simonw/archive/2007/01/17/622032.html)

```lua
--lua仿写类似C&#43;&#43;的类
--注意，lua的默认参数比较奇葩，在成员函数内部，直接添加xxx1 or xxx2即可
people = { }

function people:new( newName, newIdentity )
    obj = {}
    obj.name = newName or &#34;unknown name1&#34;
    obj.identity = newIdentity or &#34;unknown identity1&#34;
    setmetatable(obj,self)
    self.__index = self
    return obj
end

--添加成员函数
function  people:GetName()
    return self.name
end

function  people:GetIdentity()
    return self.identity
end

function  people:PrintInfo()
    print(&#34;object belong to people&#34;)
end

--student继承people，此时是相当于将people的元表拷贝到student里面了
student = people:new()

--为了避免类实例在创建的时候修改了类的成员变量，因此采用了临时表obj来作为缓冲
--毕竟Lua本身是没有Lua这个概念的，如果不采用临时Table作为缓冲，是无法把类实例和类之间的联系切断
function student:new( newName, newIdentity, newClassroom, newIndex )
    obj = people:new( newName, newIdentity )
    setmetatable(obj, self)
    --self.__index = self使得自身的属性都能被obj访问到
    self.__index = self

    --添加成员变量
    obj.classroom = newClassroom or &#34;unknown classroom2&#34;
    obj.index = newIndex or &#34;unknown index2&#34;
    return obj
end

--添加成员变量到子类里
function student:GetClassroom()
    return  self.classroom
end

function student:GetIndex()
    return  self.index
end

--重载基类的成员函数
function  student:PrintInfo()
    print(&#34;object belong to student&#34;)
end

print(&#34;startup&#34;)
temp = student:new(&#34;childname&#34;, 56579887, &#34;classroom1st&#34;, &#34;1&#34;)

--开启了下面语句之后可以发现，此时temp的数值会被temp3所影响
temp3 = student:new(&#34;childname3&#34;, 4987, &#34;classroom3rd&#34;, 10)

baseObj =people:new(&#34;basename&#34;, 110)

baseObj3 =people:new(&#34;basename3&#34;, 10000000)

print(&#34;---------打印类实例的信息--------&#34;)
print(&#34;在子类实例中调用基类成员函数&#34;)
print(temp:GetIdentity())
print(temp:GetName())

print(&#34;在子类实例中调用子类成员函数&#34;)
print(temp:GetClassroom())
print(temp:GetIndex())

temp:PrintInfo()

print(&#34;调用基类实例的成员函数&#34;)
print(baseObj:GetName())
print(baseObj:GetIdentity())

print(&#34;---------打印当前定义的基类与子类类型的数据--------&#34;)
print(&#34;以下为基类的信息&#34;)
--而name和name是在执行new函数的时候，才为其类实例追加的成员变量，
--而这些成员变量在原本的people里是没有的，因而为nil，正如people = {}
print(people.name)
print(people.name)
people:PrintInfo()

print(&#34;以下为子类类型的信息&#34;)
--name和identity是从people继承过来的，并且，在继承的时候，通过new函数临时为其创建的，因此数值有效
print(student.name)
print(student.identity)
--而classroom和index是在执行new函数的时候，才为其类实例追加的成员变量，
--而这些成员变量在原本的student是没有的，因而为nil，正如student = people:new{}
print(student.classroom)
print(student.index)
student:PrintInfo()

print(&#34;end&#34;)
--注意，在new()内，修改self里面的数值的时候，会导致类的成员变量也同时改变，因为，在Lua里面，本身是没有类这个概念的，我们只可以通过Table来模拟类似的类行为，为了绕过Lua的固有限制，我们在通过new()等函数进行类实例创建的时候，使用临时Table来存放此类的特有的成员变量以及成员变量。
--在上述代码里面，student里的classroom和index是无效数值，都为nil，在student:new()函数体内，使用了临时变量obj来存放其基类的成员变量以及成员函数，通过setmetatable(obj,self)语句，让obj拥有了student里面的成员函数以及基类的特性。而之所以需要执行student = people:new()语句，这是为了可以让student继承people的特性了，否则，所有调用了student类实例所有用到基类的方法都将无效，总而言之，这个metatable的作用有点像C&#43;&#43;里面的虚表，专门用来定位子类实例和基类之间的成员函数以及成员变量的地址的
```

### 控制Table类型的访问权限

要想实现此效果，需要使用Table里面`__index`和`__newindex`变量，更多详情请看[《用__index和__newindex来限制访问》](http://www.cnblogs.com/egmkang/archive/2011/09/27/2193096.html)，如下所示：

```lua
--访问权限控制函数
function cannotModifyHp(object)
    local proxy = {}
    local mt = {
        __index = object,
    __newindex = function(k,v)
        if k ~= &#34;hp&#34; then
        object[k] = v
        end
    end
    }
    setmetatable(proxy,mt)
    return proxy
end
 
object = {hp = 10,age = 11}
--function object.sethp(self,newhp)
--    self.hp = newhp
--end

function object:sethp(newhp)
    self.hp = newhp
end
 
o = cannotModifyHp(object)
 
o.hp = 100    --修改失效
print(o.hp)
 
o:sethp(111)    --修改失效
print(o.hp)
 
object:sethp(100)    --修改生效
print(o.hp)
```

### Lua上的语法糖收集

&gt; `v:name(args)` 可以被解析成 `v.name(v,args)` 或者 `v.name(self,args)` 

举个栗子，如下所示：

```lua
object = {hp = 10,age = 11}
function object.SetHp1st(self,newHp)
    self.hp = newHp
end

function object:SetHp2nd(newHp)
    self.hp = newHp
end
```

 - `function funcname() body end` 等效于 `funcname = function() body end`
 - `function table.a.b.c.funcname() body end` 等效于 `table.a.b.c.funcname = function() body end`
 - `local function funcname() body end` 等效于 `local funcname; funcname = function() body end` 不等效于 `local funcname = function() body end`哦

### 重载运算符Lua的运算符

在Lua之中，我们可以通过`Metatable(元表)`来重载非数值运算符，能够重载的运算符如下所示：

|  运算符   |  在Lua里的关键字   |                    意义                    |
| :----: | :----------: | :--------------------------------------: |
| &#43;  |   add    |                   加法运算                   |
| -  |   sub    |                   减法运算                   |
| *  |   mul    |                   乘法运算                   |
| /  |   div    |                   除法运算                   |
| %  |   mod    |                取模运算，获取余值                 |
| ^  |   pow    |                   幂运算                    |
| -- |   urm    |                一元**-**操作符                |
| .. |  concat  |                   连接操作                   |
| #  |   len    |           取数据类型的长度，比如table长度等            |
| == |    eq    |                 关系运算符：相等                 |
| &lt;  |    lt    |                 关系运算符：小于                 |
| &lt;= |    le    |               关系运算符：小于或等于                |
| [] |  index   |       通过下标访问`Table[key]`，仅用于读取数据       |
| [] | newindex | 通过下标`Table[key]` ，仅用于修改数据，如`Table[key] = value` |
| 未知 |   call   |             当Lua调用一个值时调用             |

以下演示了如何重载支持特定格式的表的运算符操作，如`&#43;`和`-`

```lua
--定义2个表
--a = {5, 6,10,8,69,10,&#34;hello&#34;}
a = {5, 6}
b = {7, 8}

--用c来做Metatable
c = {}
--重定义加法操作
c.__add = function(op1, op2)
--    将所有元素进行配对相加
    local result = {} 
    local lenght1 = table.maxn(op1)
    local lenght2 = table.maxn(op2)
    if lenght1 == lenght2 then
       for index,item in ipairs(op1) do
           table.insert(result,op1[index]&#43;op2[index])
       end
    end
    return result
--   --枚举op2的所有元素，将其插入到op1之中，相当于C&#43;&#43;里面的重载运算符&#43;
--   for key, item in ipairs(op2) do
--      print(&#34;key = &#34;..key)
--      print(&#34;item = &#34;..item)
--      --将item插入到op1之中
--      table.insert(op1, item)
--   end
--   return op1
end

c.__sub = function (op1,op2)
    local result = {} 
    local lenght1 = table.maxn(op1)
    local lenght2 = table.maxn(op2)
    if lenght1 == lenght2 then
       for index,item in ipairs(op1) do
           table.insert(result,op1[index]-op2[index])
       end
    end
    return result
end

--将a的Metatable设置为c
--将__add的方法应用到类b的Table数据中
--setmetatable(a, c)
setmetatable(b, c)
--d现在的样子是{5,6,7,8}
d = a &#43; b
--d = b &#43; a
for key1,value1 in ipairs(d) do
    print(value1)
end
--print(c.__add(a,b)[1])
--print(c.__add(a,b)[1])
e = a - b
--d = b &#43; a
for key2,value2 in ipairs(e) do
    print(value2)
end
```

---

### 和C&#43;&#43;相比，那些常见的知识盲区

1. Lua没有关系操作符`!=`，以`~=`来代替，其它的倒是和C&#43;&#43;一样

```lua
--如果a不为10的话，那么打印a
if a ~= 10 then
  print(a)
end
```

2. Lua的函数体不要用`{body}`来包裹起来，只需要在后面添加上`end`即可，另外在Lua之中，`if`代码块不需要用`(body)`来包裹起来，只需要在判断之中，通过空格区分开来，然后在后面添加`then`即可，注意，有`elseif`而没有`else if`的语句哦，比如`if exp then body end` 和 `elseif exp then body`

```lua
function IsMatchedID(oldID, newID)
    if oldID == newID then
        return true
    elseif oldID &gt; newID then
        print(&#34;oldID &gt; newID&#34;)
        return false
    else
        print(&#34;oldID &lt; newID&#34;)
        return false
    end
end

print(IsMatchedID(10,13))
print(IsMatchedID(10,10))
print(IsMatchedID(18,10))
```

3. 获取Table、字符串等类型的长度，不需要`length()`等函数来获取，直接用*#*即可

```lua
tempTable = {897,8,78,7,87,87,8,78,78,7,87,8,7}
print(#tempTable)
tempString = &#34;fadfadfadf&#34;
print(#tempString)
```

---

### 参考资料
 - [云风的 BLOG](https://blog.codingnow.com/)
 - [Lua 教程](https://www.runoob.com/lua/lua-tutorial.html)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:54598/posts/lua_learn_notes/  

