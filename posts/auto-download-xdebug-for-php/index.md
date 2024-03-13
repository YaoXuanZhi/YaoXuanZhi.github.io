# PHP-XDebug安装脚本


### 前言
目前在项目里做导表工具改版，由于这个导表工具采用php实现的，希望在ide上可以断点调试php，需要安装xdebug

在idea里的php settings上有其安装指导，如下图所示：

![](/assets/2019-09-01/1567309969314.png)

经过网上检索可知，安装php-xdebug的步骤如下：
 - 1. 查询phpinfo
 - 2. 拿phpinfo去[xdebug wizard](https://xdebug.org/wizard.php)查询其匹配的xdebug版本及其下载链接
 - 3. 然后下载dll并在`php.ini`里修改xdebug的配置项

但由于本人有多台办公设备，并且其php版本都不太一样，所以需要开发一个快速下载/部署xdebug的脚本，能达到一键部署php的开发调试环境就最好了

### 基本思路
 - 1. ~~在shell上用`which php`查找到php的所在路径，并`cd $(dirname $(which php))`~~（将这个py脚本放置在php.exe的同级目录就行）
 - 2. ~~判断这个目录下是否有`php.ini`文件，如果没有的话，就`cp php.development php.ini`~~（这部分采用python来实现）
 - 3. ~~在shell上执行`php -i phpinfo(); &gt; phpinfo.txt`，将结果输出到文本上~~（在python中调用此shell指令，并将输出存放在变量中）
 - 4. 用输出的phpinfo信息粘贴在[xdebug.org-custom installation instructions](https://xdebug.org/wizard.php)上的文本框上查询，这里面会检测到xdebug版本
   - ![在wizard上查询xdebug版本](/assets/2019-09-01/1567312543380.png)
     - 具体的phpinfo的检测条件在[phpinfo-scanner.php](https://github.com/derickr/xdebug.org/blob/master/html/include/phpinfo-scanner.php)
       &gt;这一步是关键，分析这个查询请求的`Responses Headers`和`Form Data`
     - ![](/assets/2019-09-01/1567314064999.png)
 - 5. 采用bs4从html上提取php_xdebug_xxx.dll的下载链接
 - 6. 下载php_xdebug_xxx.dll到ext目录下
 - 7. 采用configparser来修改php.ini上的xdebug配置

### 代码实现

```py
# !/user/bin/env python
# -*- coding:utf-8 -*- 

import os, sys, subprocess, locale, shutil
import requests, codecs, urllib, configparser
from bs4 import BeautifulSoup
from sys import stdout

# 拿phpinfo在xdebug.org上查询并提取xdebug.dll的下载链接
def get_xdebug_download_page(phpinfo):
    url = &#39;https://xdebug.org/wizard&#39;
    data = {
        &#34;data&#34;: phpinfo,
        &#34;submit&#34;: &#34;Analyse my phpinfo() output&#34;
    }
    headers = {
        &#34;User-Agent&#34;: &#34;Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36&#34;,
        &#34;Origin&#34;: &#34;https://xdebug.org&#34;,
        &#34;Content-Type&#34;: &#34;application/x-www-form-urlencoded&#34;,
        &#34;Accept&#34;: &#34;text/html,application/xhtml&#43;xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3&#34;,
        &#34;Referer&#34;: &#34;https://xdebug.org/wizard&#34;,
        &#34;Accept-Encoding&#34;: &#34;gzip, deflate, br&#34;,
        &#34;Accept-Language&#34;: &#34;zh-CN,zh;q=0.9&#34;,
    }
    return requests.post(url=url, headers=headers, data=data).text

# 从html上提取xdebug的下载链接
def find_xdebug_download_link(html):
    soup = BeautifulSoup(html, features=&#34;html.parser&#34;)
    return soup.find(&#34;ol&#34;).find(&#34;a&#34;)[&#34;href&#34;]

# 获取phpinfo
def get_php_info():
    phpinfo = read_commandline(&#34;php -i phpinfo();&#34;)
    substr_flag = phpinfo.find(&#34;___________________&#34;)
    return phpinfo[0:substr_flag]

# 获取shell的输出结果
def read_commandline(command_str):
    output_str = &#34;&#34;
    ps = subprocess.Popen(command_str, stdin=subprocess.PIPE, stdout=subprocess.PIPE, shell=True)
    while True:
        data = ps.stdout.readline()
        if data == b&#39;&#39;:
            if ps.poll() is not None:
                break
        else:
            output_str = output_str &#43; data.decode(codecs.lookup(locale.getpreferredencoding()).name)
    return output_str

# 下载文件
def download_file(url, file_path):
    r = requests.get(url)
    with codecs.open(file_path,&#39;wb&#39;) as f:
        f.write(r.content)

# Refer to : https://www.jb51.net/article/167786.htm
def download_file_with_process(url, file_path):
    with codecs.open(file_path, &#34;wb&#34;) as fw:
        with requests.get(url, stream=True) as r:
            filesize = r.headers[&#34;Content-Length&#34;]
            chunk_size = 128
            times = int(filesize) // chunk_size
            show = 1 / times
            show2 = 1 / times
            start = 1
            for chunk in r.iter_content(chunk_size):
              fw.write(chunk)
              if start &lt;= times:
                stdout.write(f&#34;download process : {show:.2%}\r&#34;)
                start &#43;= 1
                show &#43;= show2

def check_php_cfg(php_cfg):
    default_php_cfg = php_cfg&#43;&#34;-development&#34;
    if not os.path.exists(php_cfg):
        shutil.copy(default_php_cfg, php_cfg)

# 更新php.ini上的xdebug配置
def update_php_cfg(php_cfg, xdebug_path):
    conf = configparser.ConfigParser()
    conf.read(php_cfg)

    xdebug_section_name = &#34;xdebug&#34;
    if conf.has_section(xdebug_section_name):
        pass
    else:
        conf.add_section(xdebug_section_name)
    conf.set(xdebug_section_name, &#34;zend_extension&#34;, xdebug_path)
    conf.set(xdebug_section_name, &#34;xdebug.remote_enable&#34;, &#34;1&#34;)
    conf.set(xdebug_section_name, &#34;xdebug.remote_autostart&#34;, &#34;1&#34;)
    # conf.set(xdebug_section_name, &#34;xdebug.remote_host&#34;, &#34;10.0.2.2&#34;)
    # conf.set(xdebug_section_name, &#34;xdebug.remote_port&#34;, &#34;9000&#34;)

    outfile = codecs.open(php_cfg, &#34;w&#34;)
    conf.write(outfile)
    outfile.close()

if __name__ == &#39;__main__&#39;:
    php_cfg = &#34;php.ini&#34;

    print(&#34;-------------check php.ini---------------&#34;)
    check_php_cfg(php_cfg)

    print(&#34;-------------fetch phpinfo---------------&#34;)
    phpinfo = get_php_info()

    print(&#34;-----fetch xdebug.dll download link------&#34;)
    html = get_xdebug_download_page(phpinfo)
    download_link = find_xdebug_download_link(html)

    print(&#34;----------download xdebug.dll------------&#34;)
    dir_path = &#34;./ext&#34;
    file_name = os.path.basename(download_link) 
    file_path = os.path.join(dir_path, file_name)
    full_file_path = os.path.abspath(file_path)
    if not os.path.exists(file_path):
        # download_file(download_link, full_file_path)
        download_file_with_process(download_link, full_file_path)
    else:
        print(&#34;%(full_file_path)s already exists, skip download ……&#34; % {&#39;full_file_path&#39; : full_file_path})

    update_php_cfg(php_cfg, full_file_path)
```

附件：[php_xdebug_deploy.zip](/assets/2019-09-01/php_xdebug_deploy.zip)

### 注意事项
由于此脚本是基于xdebug.org里 **[custom installation instructions](https://xdebug.org/wizard.php)** 提供的查询功能来实现的，如果这个站点的规则改动了，则脚本可能会失效

&gt;ps:其实在刚编写此脚本的时候，也曾考虑过采用`selenium&#43;headless browser()`来处理ajax请求，不过考虑到这个脚本做的就是减法，用headless browser的方案会引入第三方可执行程序，比如`chrormedriver.exe`，这就太不友好了，所以还是自己分析下ajax请求，提取到`[Analyse my phpinfo() output] Button`的真正http请求

---

### 参考资料
 - [Python3 requests文件下载 期间显示文件信息和下载进度代码实例](https://www.jb51.net/article/167786.htm)
 - [Python爬虫之selenium库(三)：Chrome无头模式与操作浏览器](https://blog.csdn.net/bqw18744018044/article/details/81351137)
 - [selenium&#43;Headless Chrome实现不弹出浏览器自动化登录--问题总结](https://blog.csdn.net/qq_26803795/article/details/82467037)
 - [selenium设置谷歌无头浏览器](https://blog.csdn.net/jjjndk1314/article/details/80540204)
 - [downloading-with-chrome-headless-and-selenium](https://stackoverflow.com/questions/45631715/downloading-with-chrome-headless-and-selenium)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://example.org/posts/auto-download-xdebug-for-php/  

