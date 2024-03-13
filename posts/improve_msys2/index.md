# 拓展Msys2


由于新工作的服务器开发环境相对麻烦，遂编写了一个集成脚本来简化了服务器环境部署和运行流程

&lt;!--more--&gt;

## 前言
新项目的服务器工程其实是skynet for windows，基于msys2&#43;gcc来进行编译并运行，因此，日常开发工作中，需要经常打开msys2终端来输入各种shell指令，这并不方便

另外，本人更加偏好有个集成好的bat脚本，在上面输入【1】【2】【3】等数字之后，就能执行相应的【构建】【启动】【测试】等命令，最终调用msys2上的类linux环境来执行透传过来的命令，而且这种脚本也可以同配套环境打包给策划、测试童鞋，让他们快捷自建服务器完成功能验收和测试

## 为msys2的追加镜像源
基于 [《MSYS2 镜像使用帮助》](https://mirrors.tuna.tsinghua.edu.cn/help/msys2/)提供的实现思路，最初使用Python来快速实现了一个脚本

&lt;details&gt;
  &lt;summary&gt;append_extra_mirror_source.py&lt;/summary&gt;

```python
# !/user/bin/env python
# -*- coding:utf-8 -*- 
import os, glob, codecs, re, argparse

def add_mirror_url(mirror_url):
    mirror_paths = glob.glob(&#34;mirrorlist*&#34;)

    re_pattern = r&#39;(Server = )(https.*.org)(.*)&#39;

    for path in mirror_paths:
        with codecs.open(path, mode=&#34;r&#34;, encoding=&#34;utf-8&#34;, errors=&#39;ignore&#39;) as f:
            config = f.read()
            f.close()
            search_re = re.search(re_pattern, config, re.M|re.I)
            exist_same_re = re.search(mirror_url, config, re.M|re.I)
            if not exist_same_re and search_re:
                mirror_url_item = search_re.group()
                temp = re.compile(re_pattern)
                mirror_url_item = temp.sub(r&#39;\1%(mirror_url)s\3&#39; % {&#39;mirror_url&#39;: mirror_url}, mirror_url_item)
                new_config = &#39;%(mirror_url)s\n%(source)s&#39; % {&#39;mirror_url&#39;: mirror_url_item, &#39;source&#39;: config}
                outfile = codecs.open(path, &#39;w&#39;, &#39;utf-8&#39;)
                outfile.write(new_config)
                outfile.close()

if __name__ == &#39;__main__&#39;:
    parser = argparse.ArgumentParser()
    parser.add_argument(&#34;-url&#34;, &#34;--mirror_url&#34;, help=&#34;输入其他镜像源&#34;, default=&#34;https://mirrors.cloud.tencent.com/msys2&#34;)
    parser.add_argument(&#34;-msys2&#34;, &#34;--msys2_dir&#34;, help=&#34;msys2安装目录&#34;, default=&#34;C:/msys64&#34;)

    args = parser.parse_args()

    pacman_mirror_dir = &#34;%s/etc/pacman.d&#34; % (args.msys2_dir)
    os.chdir(pacman_mirror_dir)
    add_mirror_url(args.mirror_url)
```

&lt;/details&gt;

但考虑到后续会推广给不会撸码的童鞋使用这个集成脚本，而这些童鞋大部分都是首次安装msys2的，因此，基于Python实现的脚本显然是不行的，又重新实现了一个bash脚本

由于bash脚本本身对文件、字符串等操作的支持极其有限，在此使用了msys2内置的sed、cp、cat、grep等工具来实现

在编写这个bash脚本的时候，遇到了一些知识盲区，特意记录下来：
 - ✔ grep正则查找
 - ✔ sed正则替换需要对双引号、小括号等特殊字符进行前置转义
 - ✔ 在shell函数内修改传参变量的内容
 - ✔ 在文件首行插入新内容

&lt;details&gt;
  &lt;summary&gt;append_extra_mirror_source.sh&lt;/summary&gt;

```sh
#!/bin/bash

# sed 需要对斜杆和括号等字符进行转义后才能正常正则替换
replace_escape_string(){
    eval $2=\&#34;$(eval echo &#34;\${$1}&#34; | sed &#34;s/\//\\\\\//g; s/(/\\\\(/g; s/)/\\\\)/g&#34;)\&#34;
}

# 在文件头上加入其它镜像源
push_front_mirror_url(){
    _source_config_file=$1
    _backup_config_file=$(dirname $1)/bak_$(basename $1)
    _old_mirror_pattern=$2
    _new_mirror_host=$3

    # 备份原本的镜像源配置
    if [ ! -f ${_backup_config_file} ]; then
        cp $_source_config_file ${_backup_config_file}
    fi

    # 找到镜像源配置模板
    mirror_temp_url=$(grep -r -P &#34;$_old_mirror_pattern&#34; ${_backup_config_file})

    replace_escape_string _old_mirror_pattern _old_mirror_pattern
    replace_escape_string _new_mirror_host _new_mirror_host

    # 根据模板镜像源修改成新的镜像源
    new_mirror_url=$(echo $mirror_temp_url | sed &#34;s/$_old_mirror_pattern/\\1$_new_mirror_host\\3/g&#34;)

    # 在文件头上插入新的镜像源链接
    (echo $new_mirror_url &amp;&amp; cat ${_backup_config_file}) &gt; $_source_config_file
}

main(){
    # 腾讯云镜像源
    # new_mirror_host=&#34;https://mirrors.cloud.tencent.com/msys2&#34;

    # 清华镜像源
    new_mirror_host=&#34;https://mirrors.tuna.tsinghua.edu.cn/msys2&#34;

    old_mirror_pattern=&#34;(Server.*)(http.*repo.msys2.org)(.*)&#34;

    if [ ${#} == 1 ]; then
        new_mirror_host=$1
    fi

    # 遍历pacman的镜像源配置
    for file in /etc/pacman.d/mirror*; do
        push_front_mirror_url $file $old_mirror_pattern $new_mirror_host
    done
}

main $1
```

&lt;/details&gt;

## 拓展msys2

### 让msys2支持在dos上直接执行shell指令
在着手实现过程中，以下这些点是挺有意思的：
 - ✔ 在dos中调用msys2来直接执行shell指令
   &gt;详情看msys2_shell.cmd里面的`!SHELL_ARGS!`参数，其实它实现了：一个外部参数若果并不是msys2_shell.cmd的内置参数的话，其将会原样传递到终端上，结合`bash -c cmd`即可实现外部调用
 - ✔ 如何指定msys2的启动目录
   &gt;详情看msys2_shell.cmd的参数列表：`-where dir`
 - ✔ 在msys2里，将父bash的alias传递给子bash，并且执行其它指令
   ```sh
   bash --noprofile --rcfile &lt;(alias)
   ```

### 将msys2添加到右键菜单上
 - ✔ 在目录下或者文件夹上执行该菜单命令时，启动msys2并定位到该目录下
 - ✔ 在某个文件上执行该菜单命令时，启动msys2，定位到该文件所在目录，并用Vim打开该文件

&lt;details&gt;
  &lt;summary&gt;msys2_vim_installer.bat&lt;/summary&gt;

```bat
@echo off
::判断是否已经获取了管理员身份
Md &#34;%WinDir%\System32\test_permissions&#34; 2&gt;NUL||(Echo 请使用右键管理员身份运行&amp;&amp;Pause &gt;NUL&amp;&amp;Exit)
Rd &#34;%WinDir%\System32\test_permissions&#34; 2&gt;NUL
SetLocal EnableDelayedExpansion

call %~dp0create_depends_env.bat

::设置右键菜单项的名称
set desc=&#34;Msys2-Vim&#34;
::设置搭建编译环境的BAT
set runbat=&#34;run_msys2_vim.bat&#34;
::设置子路径名称
set subname=&#34;msys2_vim&#34;
::去掉双引号，主要是为了兼容包含了空格的文件名或路径
call :removequotes runbat
call :removequotes subname

::添加注册表信息
reg add &#34;HKEY_CLASSES_ROOT\Directory\background\shell\%subname%&#34; /v &#34;&#34; /t REG_SZ /d %desc%
reg add &#34;HKEY_CLASSES_ROOT\Directory\background\shell\%subname%\command&#34; /v &#34;&#34;  /t REG_EXPAND_SZ /d &#34;\&#34;%~dp0%runbat%\&#34; \&#34;%%V\&#34;

reg add &#34;HKEY_CURRENT_USER\Software\Classes\*\shell\%subname%&#34; /v &#34;&#34; /t REG_SZ /d %desc%
reg add &#34;HKEY_CURRENT_USER\Software\Classes\*\shell\%subname%\command&#34; /v &#34;&#34; /t REG_EXPAND_SZ /d &#34;\&#34;%~dp0%runbat%\&#34; \&#34;%%V\&#34;

reg add &#34;HKEY_CLASSES_ROOT\Directory\shell\%subname%&#34; /v &#34;&#34; /t REG_SZ /d %desc%
reg add &#34;HKEY_CLASSES_ROOT\Directory\shell\%subname%\command&#34; /v &#34;&#34; /t REG_EXPAND_SZ /d &#34;\&#34;%~dp0%runbat%\&#34; \&#34;%%V\&#34;

:removequotes
FOR /F &#34;delims=&#34; %%A IN (&#39;echo %%%1%%&#39;) DO set %1=%%~A
GOTO :eof
```

&lt;/details&gt;

&lt;details&gt;
  &lt;summary&gt;run_msys2_vim.bat&lt;/summary&gt;

```bat
@echo off

call %~dp0config.bat

set ROOT=%1
call :removequotes ROOT

pushd &#34;%ROOT%&#34; 2&gt;nul &amp;&amp; (call :folder &#34;%ROOT%&#34; &amp; popd) || call :file &#34;%ROOT%&#34;
GOTO :eof

:file
call :filename ROOT file_name
call :dirname ROOT dir_name

set command_args=%msys2_type% -where &#34;%dir_name%&#34;
call %msys2_shell_bat% %command_args% -outside_exec &#34;vim &#39;%file_name%&#39;&#34;
GOTO :eof

:folder
set command_args=%msys2_type% -where &#34;%ROOT%&#34;
call %msys2_shell_bat% %command_args%
GOTO :eof

:removequotes
FOR /F &#34;delims=&#34; %%A IN (&#39;echo %%%1%%&#39;) DO set %1=%%~A
GOTO :eof

:dirname
FOR /F &#34;delims=&#34; %%A IN (&#39;echo %%%1%%&#39;) DO set %2=%%~dpA
GOTO :eof

:filename
FOR /F &#34;delims=&#34; %%A IN (&#39;echo %%%1%%&#39;) DO set %2=%%~nxA
GOTO :eof
```

&lt;/details&gt;

提供一个脚本来关联msys2和当前脚本的关系

&lt;details&gt;
  &lt;summary&gt;create_depends_env.bat&lt;/summary&gt;

```bat
@echo off
REM 创建目录关联

echo 将msys2目录粘贴到这里
set /p msys2_dir=

call :removequotes msys2_dir

@REM 自动生成config.bat
set config_path=&#34;%~dp0config.bat&#34;
if not exist %config_path% (
    echo @echo off&gt;&gt; %config_path%
    echo set msys2_install_dir=&#34;%msys2_dir%&#34;&gt;&gt; %config_path%
    echo set msys2_shell_bat=&#34;%msys2_dir%\msys2_shell.cmd&#34;&gt;&gt; %config_path%
    echo set msys2_type=-mingw64&gt;&gt; %config_path%
    echo set pause_cmd=read -p &#39;Please press any key after&#39;&gt;&gt; %config_path%
)

GOTO :eof

:removequotes
FOR /F &#34;delims=&#34; %%A IN (&#39;echo %%%1%%&#39;) DO set %1=%%~A
GOTO :eof

GOTO :eof
```

&lt;/details&gt;

最终成品：[msys2_vim](https://github.com/YaoXuanZhi/msys2_vim)

## 参考资料
 - [MSYS2 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/msys2/)
 - [forward aliases to child process](https://unix.stackexchange.com/questions/285009/forward-aliases-to-child-process)
 - [invoking-msys-bash-from-windows-cmd](https://stackoverflow.com/questions/2752974/invoking-msys-bash-from-windows-cmd)
 - [executing-msys-from-cmd-exe-with-arguments](https://stackoverflow.com/questions/2546757/executing-msys-from-cmd-exe-with-arguments)
 - [how-do-i-integrate-msys2-shell-into-visual-studio-code-on-window](https://stackoverflow.com/questions/45836650/how-do-i-integrate-msys2-shell-into-visual-studio-code-on-window/48016561#48016561)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://yaoxuanzhi.github.io/posts/improve_msys2/  

