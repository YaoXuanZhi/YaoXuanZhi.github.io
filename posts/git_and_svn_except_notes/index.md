# Git&amp;SVN爬坑


在涉及到多工种协作时，大部分开发团队采用svn作为版本管理工具，这里记录了一些本人遇到的svn的疑难杂症和相关解决方案

&lt;!--more--&gt;

---

## FAQ
#### 遇到了Failed to run the WC DB work queue associated报错日志
打开msys2，然后使用`pacman -S sqlite3`安装sqlite3.exe，进入msys2，然后执行`sqlite3 .svn/wc.db &#34;delete from work_queue&#34;`清空 svn 的工作队列

#### 遇到了svn 被锁住，报错日志： `Can’t revert without reverting children`
进入到svn的顶级目录下，然后执行`svn revert . --depth infinity`即可

#### Github超过100M的大文件上传，报错日志： `remote: error: File XXX/XXX/XXX is 234 MB; this exceeds GitHub‘s file size limit of 100.00 MB`

```sh
cd git-repo

# 设置Git LFS
git lfs install

# 追踪大文件的类型，此时会生成一个gitattributes文件
git lfs track &#34;*.zip&#34;

# 将大文件添加到版本库中
git add resources.zip
git commit -m &#34;提交.zip大文件&#34;

# 推送文件到远程仓库
git push
```

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/git_and_svn_except_notes/  

