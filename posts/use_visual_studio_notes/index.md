# Visual Studio使用笔记


宇宙第一IDE，yyds

&lt;!--more--&gt;

---

## 快捷键配置

### Visual Studio常用快捷键
|  快捷键   |  描述   |
| :----: | :----------: |
| `ctrl&#43;k`&#43;`ctrl&#43;c` |  代码注释  |
| `ctrl&#43;shift&#43;u` |  让被选中的文本里的字母全部转换为大写  |
| `ctrl&#43;u` |  让被选中的文本里的字母全部转换为小写  |
| `ctrl&#43;shift&#43;space` |  当光标在函数的小括号里面的时候，会弹出此函数的参数列表  |
| `ctrl&#43;i` |  向前导航跳转  |
| `ctrl&#43;o` |  向后导航跳转  |

### Visual Assist常用快捷键
|  快捷键   |  描述   |
| :----: | :----------: |
| `shift&#43;alt&#43;s` |  Find Symbol  |
| `shift&#43;alt&#43;f` |  Find References  |
| `alt&#43;g` |  Goto Implementation  |
| `shift&#43;alt&#43;g` |  Goto Related  |
| `shift&#43;alt&#43;o` |  Open File in Solution  |

## Visual Assist插件配置

### C&#43;&#43;注释配置

#### 枚举类型注释`[\e]`
```c&#43;&#43;
/** @brief $SymbolContext$ */
enum AnotherEnum
{
    V1,/**&lt; value 1 */
    V2 /**&lt; value 2 */
};
```

#### 函数注释`[\*]`
```c&#43;&#43;
/** 
* @brief: $SymbolContext$
* @param[in] 参数类型  参数名  参数说明
* @param[in|out] 参数类型  参数名  参数说明
* @return 返回值说明
* @note   
* $end$
*/
```

#### 类注释`[\c]`
```c&#43;&#43;
/**
* @class $ClassName$
* @brief $SymbolContext$                                                                                         
* @note   
* $end$
*/
```

#### 文件头注释`[\h]`
```c&#43;&#43;
/*****************************************************************************
*  @file     $FILE_BASE$.$FILE_EXT$
*  @brief    $SymbolContext$
*  @note
*  $end$
*
*----------------------------------------------------------------------------*
*  Change History :
* &lt;Version&gt; |                       &lt;Description&gt;
*----------------------------------------------------------------------------*
*  0.0.0.1  |                        创建头文件
*----------------------------------------------------------------------------*
*
*****************************************************************************/
```

注意，需要安装`Visual Assist`插件哦。具体需要【VAssisX】--&gt;【Tools】--&gt;【Edit VA Snippets】，然后将上面的注释宏添加到打开的`VA Snippet Editor`之中

## 其他功能

### 显示特殊变量
无需编写插件或修改配置文件，只需要将要显示的变量拉到`监视调试窗口(Watch Windows)`中，并在变量后面添加`,xxx`即可显示，目前支持以下功能

只需要将要显示的字符串拉到Watch中,并在变量后面添加,s8即可显示

|  变量显示标识   |  描述   |
| :----: | :----------: |
| ,数字  |   将变量拆分为数组显示, 数字是要显示多少位, 此法对const char*这类原始字符串非常有用    |
| ,s8  |   转换为UTF-8字符串查看   |
| ,x  |   转换成16进制查看    |
| ,hr  |   转换成Windows HRESULT解释查看    |
| ,wm  |   转换成`Windows消息`查看，例如0x0010, wm 显示 WM_CLOSE    |

---

## FAQ
### 编译错误&#39;std::tuple&#39; : too many template arguments
在VS 2012编译Gtest源码的时候，遇到了此错误，具体讨论看一下链接：[《Google Test in Visual Studio 2012》](https://stackoverflow.com/questions/12558327/google-test-in-visual-studio-2012)

解决方法

只需要在VS工程中，添加预处理` _VARIADIC_MAX=10`即可，如果此VS工程是通过qmake来生成的话，那么需要在指定的`xxx.pro`文件上添加上`DEFINES &#43;= _VARIADIC_MAX=10`语句即可。

### isnan()和isinf()未定义
此问题是本人在使用VS 2012编译log4z开源库时冒出来的，其实类似的问题集中在VC上出现，这是由于VC支持新标准不及时所引起的，详情可看：[《isnan isinf》](http://blog.csdn.net/sunmenggmail/article/details/7853081)

解决方法

在报错的所在文件头上添加上以下代码段即可：

```c&#43;&#43;
inline int isnan(double x) { return x != x; }
inline int isinf(double x) { return !isnan(x) &amp;&amp; isnan(x - x); }
```

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/use_visual_studio_notes/  

