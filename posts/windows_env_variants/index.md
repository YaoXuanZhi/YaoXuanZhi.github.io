# Windows系统环境变量


这里整理了可能会用到的Windows环境变量，方便查阅

&lt;!--more--&gt;

{{&lt; link href=&#34;https://learn.microsoft.com/zh-cn/windows/win32/procthread/environment-variables&#34; content=&#34;Windows环境变量&#34; title=&#34;documentation of FixIt Theme&#34; card=true &gt;}}

---

| 环境变量 | 含义 | 例子 |
|---|---|---|
| %USERPROFILE% | 列出当前用户Profile文件位置 | `C:\Users用户名` |
| %APPDATA% | 列出应用程序数据的默认存放位置 | `C:\Users\用户名\AppData\Roaming` |
| %LOCALAPPDATA% | 本地用户目录 | `C:\Users\用户名\AppData\Local` |
| %PATH% | 列出了可执行文件的搜索路径 | 单元格 |
| %CD% | 列出当前目录 | |
| %ALLUSERSPROFILE% | 列出所有用户Profile文件位置 | `C:\ProgramData` |
| %COMMONPROGRAMFILES% | 列出了常用文件的文件夹路径 | `C:\Program Files\Common Files` |
| %COMMONPROGRAMFILES(x86)% | 列出了常用文件的文件夹路径 | `C:\Program Files (x86)\Common Files` |
| %COMSPEC% | 列出了可执行命令外壳（命令处理程序）的路径 | `C:\Windows\System32\cmd.exe` |
| %HOMEDRIVE% | 列出与用户主目录所在的驱动器盘符 | `C:\` |
| %SYSTEMDRIVE% | 列出了Windows启动目录所在驱动器 | `C:\` |
| %HOMEPATH% | 列出用户主目录的完整路径 | `C:\Users\用户名` |
| %PROGRAMDATA% | 列出了ProgramData文件夹的路径 | `C:\ProgramData` |
| %PROGRAMFILES% | 列出了ProgramFiles文件夹的路径 | `C:\Program Files` |
| %PROGRAMFILES(X86)% | 列出了ProgramFiles(X86)文件夹的路径 | `C:\Program Files (x86)` |
| %PUBLIC% | | `C:\Users\Public` |
| %SYSTEMROOT% | 列出了Windows启动目录的位置 | `C:\Windows` |
| %TEMP% | 列出了当前登录的用户可用应用程序的默认临时目录 | `C:\Users\用户名\AppData\LocalTemp` |
| %TMP% | 列出了当前登录的用户可用应用程序的默认临时目录 | `C:\Users\用户名\AppData\LocalTemp` |
| %WINDIR% | 列出操作系统目录的位置 | `C:\Windows` |
| %OS% | 列出操作系统的名字 | `WindowsXP`和`Windows2000`列为`Windows_NT.` |
| %PATHEXT% | 列出操作系统认为可被执行的文件扩展名 | `.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC` |
| %RANDOM% | 列出界于0和32767之间的随机十进制数 | `[0, 32767]` |

### 一些所谓绿色包的制作思路
提供一个设置应用环境变量的脚本(一般命名为set_app_env.bat)，所有关联部分都采用相对路径来设置，然后再提供一个启动应用脚本(一般命名为run_app.bat)，然后将它们整体打包分发，现在各种AI应用一键运行包都是类似思路实现的

```sh
# set_app_env.bat

@echo off
set mine_root=%~dp0
set USERPROFILE=%mine_root%run_dir\User
set APPDATA=%mine_root%run_dir\User\AppData\Roaming
set app_path=%mine_root%run_dir\app_env

set path=%app_path%;%app_path%\Scripts;%path%
```

```sh
# run_app.bat

@echo off

call &#34;%~dp0set_app_env.bat&#34;

::进入当前bat的所在目录
cd /d %~dp0

# 启动一个包含某些环境变量的命令行窗口
call start cmd %1 /k

# # 启动某个python应用
# python %~dp0web_ui.py --f %1
```

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:1313/posts/windows_env_variants/  

