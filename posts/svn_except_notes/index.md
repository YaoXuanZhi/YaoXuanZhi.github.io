# SVN爬坑


在涉及到多工种协作时，大部分开发团队采用svn作为版本管理工具，这里记录了一些本人遇到的svn的疑难杂症和相关解决方案

&lt;!--more--&gt;

## FAQ
#### 遇到了svn 被锁住，报错日志： `Can’t revert without reverting children`
进入到svn的顶级目录下，然后执行`svn revert . --depth infinity`即可

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/svn_except_notes/  

