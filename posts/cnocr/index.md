# 初探Cnocr


#### 仓库地址
 - [cnocr](https://github.com/breezedeus/cnocr)

### 在线安装
 - 1. virtualenv/anaconda3
 - 2. win32OpenSSL

```sh
conda create -n cnocr python=3.7
conda activate cnocr
pip install cnocr
pip install cnstd
```

注意，执行`pip install cnstd`之后，shapely的在线安装是有问题的，需要下载离线的shapely_xxx.whl文件(如[Shapely-1.7.1-cp37-cp37m-win_amd64.whl](https://download.lfd.uci.edu/pythonlibs/w4tscw6k/Shapely-1.7.1-cp37-cp37m-win_amd64.whl))进行离线安装，如下所示：

```sh
pip uninstall Shapely
pip install your_path/Shapely-1.7.1-cp37-cp37m-win_amd64.whl
```

### 源码安装
```sh
cd cnstd/
pip install -r requestment.txt
python setup.py build
python setup.py install
```

### 离线包部署
如果不想折腾这个cnocr的环境配置，这里提供了一个本人整合的离线工具包，只需要在一个不包含中文或空格的路径上解压，直接**以管理员身份**执行**ocr_deploy.bat**即可

下载链接：[cnocr离线工具包-cnocr_toolkit.zip](https://pan.baidu.com/s/1iIPuTI3OXZca1FqcmgKYEA)，提取码：`y80u`

&gt;该离线包仅仅支持x64bit Windows系统

### 代码片段

#### 纯文本识别

```python
# 识别纯文字图片
def ocr_single_png_from_cache(image):
    nd_array = np.asarray(image.convert(&#39;RGB&#39;))
    res = cn_ocr.ocr(nd_array) 
    res = [&#39;&#39;.join(line_p) for line_p in res]
    temp_res = &#39;\n&#39;.join(res)
    return temp_res

# 识别纯文字图片
def ocr_single_png_from_cache_bak(image):
    img_bytes = BytesIO()
    image.save(img_bytes, format=&#39;PNG&#39;)
    nd_array = mx.image.imdecode(img_bytes.getvalue())
    res = cn_ocr.ocr(nd_array) 
    res = [&#39;&#39;.join(line_p) for line_p in res]
    temp_res = &#39;\n&#39;.join(res)
    return temp_res
```

#### 外部调用cnocr

```python
# 读取shell命令输出结果
def popen_wrapper(cmd_line):
    p = subprocess.Popen(cmd_line, shell=True, stdout=subprocess.PIPE)
    lines = p.stdout.readlines()[1:]
    temp_str = &#34;&#34;
    for str in lines:
        utf8_str = str.decode(&#34;utf8&#34;)
        temp_str = temp_str &#43; utf8_str
    return temp_str

# 尝试进行OCR识别
def try_ocr_image(image_path):
    cmd_line = &#34;xxx/try_ocr_image.bat %s&#34; % (image_path)
    ocr_str = popen_wrapper(cmd_line).strip()
    return ocr_str
```

#### 剪贴板OCR

```python
# -*- coding: utf-8 -*-
import win32.win32clipboard as win32clipboard
from PIL import Image, ImageDraw, ImageGrab
import os

# 读取PIL image进行OCR识别
def ocr_image_from_pil(image):
    from cnocr import CnOcr 
    import numpy as np
    cn_ocr = CnOcr() 
    nd_array = np.asarray(image.convert(&#39;RGB&#39;))
    res_lines = cn_ocr.ocr(nd_array) 
    res = [&#39;&#39;.join(line_p) for line_p in res_lines]
    temp_res = &#39;\n&#39;.join(res)
    return temp_res

# 读取剪贴板上的图像数据进行OCR识别
def try_ocr_clipboard():
    ocr_result = &#34;&#34;
    im = ImageGrab.grabclipboard()
    if not im == None:
        return ocr_image_from_pil(im)
    return ocr_result

def set_text_to_clipboard(text):
    text_bytes = bytes(text, encoding=&#34;utf8&#34;)
    win32clipboard.OpenClipboard()
    win32clipboard.SetClipboardText(text)
    win32clipboard.CloseClipboard()

def ocr_clipboard():
    ocr_result = try_ocr_clipboard()
    if (len(ocr_result) &gt; 0):
        set_text_to_clipboard(ocr_result)
        print(&#34;剪贴板OCR结果：\n%s&#34; % (ocr_result))
        os.system(&#34;pause&#34;)

ocr_clipboard()
```

 - [剪贴板OCR工具-ocr_clipboard.zip](/assets/2021-04-11/ocr_clipboard.zip)

 &gt;注意，Tim上有一个屏幕OCR功能，只需要Ctrl&#43;Alt&#43;O即可唤出，识别准度更高

#### 训练新字体

##### 生成新字体的训练集
先通过以下脚本来快速生成字体的训练集

&lt;details&gt;
  &lt;summary&gt;pre_train_for_font.py&lt;/summary&gt;

```python
# !/user/bin/env python
# -*- coding:utf-8 -*- 

import os
import argparse
from PIL import Image,ImageFont,ImageDraw
  
# 根据文本生成图片
def save_chars_image(text, image_path, font, is_debug = False):
    chars_x, chars_y = 0, 0
    chars_w, chars_h = font.getsize(text)
  
    if is_debug == True:
        chars_w = chars_w &#43; 2
        chars_h = chars_h &#43; 2
  
    im = Image.new(&#34;RGB&#34;, (chars_w, chars_h), (255, 255, 255))
    dr = ImageDraw.Draw(im)
  
    # 绘制文字边框
    if is_debug == True:
        coords = [(chars_x&#43;1, chars_y&#43;1), (chars_x&#43;1, chars_y&#43;chars_h-1),
                (chars_x&#43;chars_w-1, chars_y&#43;chars_h-1), (chars_x&#43;chars_w-1,chars_y&#43;1)]
        dr.polygon(coords, outline=(255, 0, 10))
  
    # 居中绘制文字
    dr.text((chars_x, chars_y), text, font=font, fill=(0,0,0), align=&#39;center&#39;)
    im.save(image_path)
 
def indexing(standards, new_chars, text):
    res = []
    for i in range(len(text)):
        try:
            res.append(standards.index(text[i])&#43;1)
        except:
            new_chars.append(text[i])
            res.append(len(standards)&#43;len(new_chars)&#43;1)
    return res
  
def clear_invalid_chars(char_array):
    for i in range(len(char_array)):
        char_array[i] = char_array[i].strip(&#39;\n&#39;)

def main():
    parser = argparse.ArgumentParser(description=&#39;生成用于CnOcr训练的数据集&#39;)

    parser.add_argument(&#34;-root&#34;, &#34;--root_dir&#34;,
        default=&#34;data&#34;,
        type=str,
        help=&#34;预训练配置目录&#34;,
	)

    parser.add_argument(&#34;-examples&#34;, &#34;--examples_dir&#34;,
        default=&#34;examples&#34;,
        type=str,
        help=&#34;图片样本所在目录&#34;,
	)

    parser.add_argument(&#34;-font&#34;, &#34;--font_path&#34;,
        default=&#34;fonts/卷卷桃心中文字体.ttf&#34;,
        type=str,
        help=&#34;待训练的字体路径&#34;,
	)

    parser.add_argument(&#34;-font_size&#34;, &#34;--font_size&#34;,
        default=20,
        type=int,
        help=&#34;待训练的字体大小&#34;,
	)

    parser.add_argument(&#34;-label&#34;, &#34;--label_path&#34;,
        default=&#34;label_cn.txt&#34;,
        type=str,
        help=&#34;文本原料&#34;,
	)

    parser.add_argument(&#34;-train&#34;, &#34;--train_name&#34;,
        default=&#34;train.txt&#34;,
        type=str,
        help=&#34;训练样本名&#34;,
	)

    parser.add_argument(&#34;-test&#34;, &#34;--test_name&#34;,
        default=&#34;test.txt&#34;,
        help=&#34;测试样本名&#34;,
	)

    parser.add_argument(&#34;-is_test&#34;, &#34;--is_test&#34;,
        action=&#34;store_true&#34;,
        help=&#34;是否生成测试图片&#34;,
	)

    parser.add_argument(&#34;-test_text&#34;, &#34;--test_text&#34;,
        default=&#34;&#34;,
        help=&#34;测试文本&#34;,
	)

    args = parser.parse_args()

    root_dir = args.root_dir
    images_dir = args.examples_dir
  
    label_path = args.label_path
    train_path = args.train_name
    test_path = args.test_name
  
    font = ImageFont.truetype(args.font_path, args.font_size)
 
    label_file = open(label_path, &#39;r&#39;, encoding=&#39;utf-8&#39;)
 
    train_file = open(os.path.join(root_dir, train_path), &#39;w&#39;, encoding=&#39;utf-8&#39;)
    test_file = open(os.path.join(root_dir, test_path), &#39;w&#39;, encoding=&#39;utf-8&#39;)
 
    standards = label_file.readlines()
    clear_invalid_chars(standards)
  
    new_chars = []
 
    label_file.close()

    if args.is_test and len(args.test_text) &gt; 0:
        image_path = &#34;test.png&#34;
        save_chars_image(args.test_text, image_path, font=font)
        return
  
    # 生成用于训练的图片集
    for i in range(len(standards)):
        text = standards[i]
        idxes = indexing(standards, new_chars, text)
 
        cnt = &#34;train_%06d.jpg&#34; % (i&#43;1)
        image_path = os.path.join(images_dir, cnt)
        save_chars_image(text, image_path, font=font)
  
        for idx in idxes:
            cnt = cnt &#43; &#34; {}&#34;.format(idx)
        train_file.write(cnt&#43;&#39;\n&#39;)
    train_file.close()
 
    # 生成用于测试的图片集
    for i in range(len(standards)):
        if (i&#43;1) % 30 == 0:
            text = standards[i]
            idxes = indexing(standards, new_chars, text)
 
            cnt = &#34;test_%06d.jpg&#34; % (i&#43;1)
            image_path = os.path.join(images_dir, cnt)
            save_chars_image(text, image_path, font=font)
  
            for idx in idxes:
                cnt = cnt &#43; &#34; {}&#34;.format(idx)
            test_file.write(cnt&#43;&#39;\n&#39;) 
    test_file.close()
 
    # 追加新增字符
    label_file = open(label_path, &#39;a&#39;, encoding=&#39;utf-8&#39;)
    for new_char in new_chars:
        label_file.write(new_char&#43;&#39;\n&#39;)
    label_file.close()

if __name__ == &#39;__main__&#39;:
    main()
```

&lt;/details&gt;

以下是生成某种字体对应的训练集的批处理

```bat
@echo off

call &#34;%~dp0..\set_ocr_env.bat&#34;

cd /d %~dp0

python scripts/pre_train_for_font.py -font_size 40 -font fonts/中国式手写风字体.ttf -root data/sample-data

pause
```

##### 在已有的模型上迭代训练

官方在[cnocr-训练自己的模型](https://github.com/breezedeus/cnocr#训练自己的模型)下已经介绍基本的训练流程了，但是这里面有一些训练的细节没有提及

```makefile
train:
    python scripts/cnocr_train.py --gpu 0 --emb_model_type $(EMB_MODEL_TYPE) --seq_model_type $(SEQ_MODEL_TYPE) \
        --optimizer adam --epoch 20 --lr 1e-4 \
        --train_file $(REC_DATA_ROOT_DIR)/sample-data_train --test_file $(REC_DATA_ROOT_DIR)/sample-data_test
```

根据这个训练参数`--epoch 20`来看，会进行20次全量训练，并且由于没有指定`--load_epoch nums`，所以是从头开始训练的，那么要想在已有的训练模型上进行增量训练集的训练，应该怎么做呢？

使用`--load_epoch start_times --epoch times`，会从指定的迭代模型上，对增量训练集进行迭代训练

##### 使用某次迭代的训练模型来进行OCR识别
使用`--model-epoch times`参数来进行OCR识别，这个参数在**evaluate**也有使用到

为了在windows下也方便进行迭代训练，这边基于官方提供的Makefile文件，将其改造成了一个[Makefile.bat](https://github.com/YaoXuanZhi/cnocr/blob/master/Makefile.bat)

&lt;details&gt;
  &lt;summary&gt;Makefile.bat&lt;/summary&gt;

```bat
@echo off

call &#34;%~dp0..\set_ocr_env.bat&#34;

title cnstd-train

set MXNET_CPU_WORKER_NTHREADS=2
set DATA_ROOT_DIR=data/sample-data
set REC_DATA_ROOT_DIR=data/sample-data-lst
set IMAGES_DIR=examples

:: `EMB_MODEL_TYPE` 可取值：[&#39;conv&#39;, &#39;conv-lite-rnn&#39;, &#39;densenet&#39;, &#39;densenet-lite&#39;]
set EMB_MODEL_TYPE=conv-lite
:: `SEQ_MODEL_TYPE` 可取值：[&#39;lstm&#39;, &#39;gru&#39;, &#39;fc&#39;]
set SEQ_MODEL_TYPE=fc
set MODEL_NAME=%EMB_MODEL_TYPE%-%SEQ_MODEL_TYPE%

@REM ------------------------------------------------------------------

:do

cls

echo 1: gen_lst

echo 2: gen_rec

echo 3: train

echo 4: evaluate

echo 5: predict

echo 6: package

echo 7: upload

set /p o=

if %o%==1 goto gen_lst

if %o%==2 goto gen_rec

if %o%==3 goto train

if %o%==4 goto evaluate

if %o%==5 goto predict

if %o%==6 goto package

if %o%==7 goto upload

goto end

@REM ------------------------------------------------------------------

:: 产生 *.lst 文件
:gen_lst
	echo python scripts/im2rec.py --list --num-label 20 --chunks 1 --train-idx-fp %DATA_ROOT_DIR%/train.txt --test-idx-fp %DATA_ROOT_DIR%/test.txt --prefix %REC_DATA_ROOT_DIR%/sample-data
	python scripts/im2rec.py --list --num-label 20 --chunks 1 --train-idx-fp %DATA_ROOT_DIR%/train.txt --test-idx-fp %DATA_ROOT_DIR%/test.txt --prefix %REC_DATA_ROOT_DIR%/sample-data

pause
GOTO do

:: 利用 *.lst 文件产生 *.idx 和 *.rec 文件。
:: 真正的图片文件存储在 `examples` 目录，可通过 `--root` 指定。
:gen_rec
	echo python scripts/im2rec.py --pack-label --color 1 --num-thread 1 --prefix %REC_DATA_ROOT_DIR% --root %IMAGES_DIR%
	python scripts/im2rec.py --pack-label --color 1 --num-thread 1 --prefix %REC_DATA_ROOT_DIR% --root %IMAGES_DIR%

pause
GOTO do

:: 训练模型
:: 如果要从某次训练模型开始继续迭代训练，通过 `--load_epoch start_times --epoch times` 指定
:train
	python scripts/cnocr_train.py --gpu 0 --emb_model_type %EMB_MODEL_TYPE% --seq_model_type %SEQ_MODEL_TYPE% --optimizer adam --epoch 20 --lr 1e-4 --train_file %REC_DATA_ROOT_DIR%/sample-data_train --test_file %REC_DATA_ROOT_DIR%/sample-data_test

pause
GOTO do

:: 在测试集上评估模型，所有badcases的具体信息会存放到文件夹 `evaluate/%MODEL_NAME%` 中
:: 指定某次迭代训练模型来进行评估，通过 `--model-epoch 1` 指定
:evaluate
	python scripts/cnocr_evaluate.py --model-name %MODEL_NAME% --model-epoch 1 -v -i %DATA_ROOT_DIR%/test.txt --image-prefix-dir examples --batch-size 128 -o evaluate/%MODEL_NAME%

pause
GOTO do

:predict
	python scripts/cnocr_predict.py --model_name %MODEL_NAME% --file examples/rand_cn1.png

pause
GOTO do

:package
	python setup.py sdist bdist_wheel

pause
GOTO do

:upload
	set VERSION=1.2.2
	python -m twine upload  dist/cnocr-%VERSION% --verbose

pause
GOTO do

:end
pause
GOTO :eof
```

&lt;/details&gt;

### 学习计划
 - ✔ 跑通cnocr的识别流程
 - ✔ 制作成离线部署包，方便在其它电脑上快速部署
 - ✔ 用来制作一些自用小工具，比如剪贴板上的图片OCR识别等
 - ✔ 跑通它的训练流程，持续改善识别精度
   - ✔ 提供新字体的预训练脚本
   - ✔ 提供适合在windows下使用的脚本-Makefile.bat
 - ✔ 尝试改进它的文本方向纠正效果
   &gt;现在它的文本校正方向很容易出现180度倒转的情况，看有没有啥办法处理下，已经找到问题了，OpenCv4.5的bug，通过`pip install opencv-python==4.4.0.46`降版本就行了，不过竖版文本的识别官方是暂不支持的，这个要另外项办法了

### 后记
在Python3.8下部署时候遇到问题，因此推荐使用Python3.7及其以下版本，详情看官方教程

个人比对了一下各个ocr项目，cnocr对纯文本图片的识别的准确度非常高，并且速度较快，但限于文本方向必须正确，一旦出现文本垂直或水平翻转，精度会严重下降；另外它对于那些广告图的识别效果也较差，不过无论如何，肯定甩tesseract不止一条街

如果你的需求大多集中在广告图等非纯文本图片上，那么建议使用paddleocr或chinesecor-lite，不过这些ocr项目的部署也相对复杂多了

&gt;ps:mxnet库有个Warning一直很烦人，但是其不影响使用，直接在`site-packages\mxnet\symbol\symbol.py`里注释掉`line 925`的warning输出

#### 离线工具包制作
python3环境(从anaconda3中提取而得) &#43; 系统环境变量关联脚本 &#43; 官方模型

---

### 参考资料
 - [更轻量的中文OCR—— cnocr-V1.2.2 ：最小模型只有 4.7M](https://zhuanlan.zhihu.com/p/134115239)
 - [OCRSpace](https://ocr.space/)
   &gt;支持超长图片的OCR识别

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: https://yaoxuanzhi.github.io/posts/cnocr/  

