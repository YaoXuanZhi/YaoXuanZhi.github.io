# 字符图片合并


在ocr识别当中，单字识别效果一般不够准确，另外，对于一个ocr功能而言，最耗时的莫过于每次执行ocr识别了，假设现今有几百张字符图片，为了快速生成`{图片名：字符}`的映射关系，需要耗时更少的方案

&lt;!--more--&gt;

这个时候图片合并就需要安排上了，将几百次的字符图片识别转换为识别若干次单行或多行文字图片，减少ocr识别次数，就能有效减少耗时了，另外，经过合并后的文字图片在保留合理的字符间隔后，其识别准度也会有所提升，可谓一箭双雕

---

### 合并成单行文字图片
实现思路上还是比较简单的，大致拆分成以下步骤：
 - 1. 设定单行文字图片里的文字数量
 - 2. 遍历文件夹下的字符图片，存储即将进行图片合并的图片路径列表
 - 3. 计算每个字符图片的尺寸大小
 - 4. 根据单行文字数量和尺寸大小来计算出最终合并后的图片尺寸，并在内存里创建一张该尺寸的纯色背景图
     &gt;注意，如果字符图片的尺寸过小，为了让各个字符之间有足够的间隔，最好增加一个参数来拓展字符图片的边界
 - 5. 计算每张字符图片的混合贴图的位置信息，将各个字符图片平铺到这个背景图片上
 - 6. 保存图片到磁盘上

```python
# 合并成单行文字图片
def combine_image_with_lite(paths, merge_times, is_debug = False):
    count = len(paths)
    src_image = Image.open(paths[0])
    image_width, image_height = src_image.size 
    x = image_width &#43; extra_bound_as_single_line
    y = image_height &#43; extra_bound_as_single_line

    # 计算合并之后的大小
    full_x = x * count
    full_y = y
    full_size = (full_x, full_y)
    full_image = Image.new(&#39;RGBA&#39;, full_size, (255,255,255))
    full_image.format = &#34;PNG&#34;
    # print(&#34;full rect : {%d,%d,%d,%d}&#34; % (0, 0, full_x, full_y))

    # 将图片块平铺到这个大图片上
    start_x = 0
    start_y = 0
    end_x = x
    end_y = y
    index = 1
    
    draw = ImageDraw.Draw(full_image)
    for path in paths:
        if is_debug == True:
            # 绘制每个填充区域的边界
            print(&#34;{%d,%d,%d,%d}&#34; % (start_x, start_y, end_x, end_y))
            draw.rectangle((start_x&#43;1, start_y&#43;1, end_x-1, end_y-1), outline = get_random_color())

            # 居中绘制填充的图片索引
            index_str = &#34;%d&#34;%(index)
            font_size_x, font_size_y = draw.textsize(index_str)
            draw.text((start_x &#43; (x-font_size_x)/2, start_y &#43; (y-font_size_y)/2), index_str, fill=&#39;black&#39;)
        else:
            # 由于字符图片边界过小，如果直接平铺的话，就会显得太挤，OCR识别效果很差
            # 为此拓展字符图片的填充区域，并将图片在此中间叠加上去
            src_image = Image.open(path)
            start_x0 = start_x &#43; (x-image_width)//2
            start_y0 = start_y &#43; (y-image_height)//2
            end_x0 = start_x0 &#43; image_width
            end_y0 = start_y0 &#43; image_height
            full_image.paste(src_image, (start_x0, start_y0, end_x0, end_y0), src_image)

        index = index &#43; 1
        if end_x &gt;= full_x:
            # 从左往右填充，遇到边界之后切换到下一行
            start_x = 0
            end_x = start_x &#43; x

            start_y = start_y &#43; y
            end_y = start_y &#43; y
        else:
            # 继续从左往右平铺
            start_x = start_x &#43; x
            end_x = end_x &#43; x

    current_dir = os.getcwd()
    output_path = &#39;%s/../single_line_%d.png&#39; % (current_dir, merge_times)
    full_image.save(output_path)
```

### 合并成多行文字图片
实现思路上同上述差不多，但是需要注意计算好图片的**行列数**，尽可能减少空白区域，步骤如下：
 - 1. 遍历文件夹下的所有字符图片得到待合并的图片路径列表
 - 2. 计算每个字符图片的尺寸大小
 - 3. 根据合并的图片数量，开根号后计算行列数
 - 4. 根据行列数来计算出最终合并后的图片尺寸，并在内存里创建一张该尺寸的纯色背景图
     &gt;注意，如果字符图片的尺寸过小，为了让各个字符之间有足够的间隔，最好增加一个参数来拓展字符图片的边界
 - 5. 计算每张字符图片的混合贴图的位置信息，将各个字符图片平铺到这个背景图片上，如果当前行已经平铺满了，就切换到下一行继续平铺
     &gt;平铺顺序：`从左到右 从上到下`
 - 6. 保存图片到磁盘上

```python
# 合并成多行文字图片
def combine_image_with_multi(paths, merge_times, is_debug = False):
    count = len(paths)
    src_image = Image.open(paths[0])
    image_width, image_height = src_image.size 
    x = image_width &#43; extra_bound_as_multi_line
    y = image_height &#43; extra_bound_as_multi_line

    # 根据待合并的图片总数来计算平铺的行列数
    sqrt2 = math.sqrt(count)
    row_num = round(sqrt2)
    column_num = row_num
    if sqrt2 &gt; column_num:
        column_num = column_num &#43; 1 
    print(&#34;总数：%d 开根号：%f 行：%d 列：%d&#34; % (count, sqrt2, row_num, column_num))

    # 计算合并之后的图片尺寸
    full_x = x * column_num
    full_y = y * row_num
    full_size = (full_x, full_y)
    full_image = Image.new(&#39;RGBA&#39;, full_size, (255,255,255))
    print(&#34;full rect : {%d,%d,%d,%d}&#34; % (0, 0, full_x, full_y))

    # 将图片块平铺到这个大图片上
    start_x = 0
    start_y = 0
    end_x = x
    end_y = y
    index = 1
    
    draw = ImageDraw.Draw(full_image)
    for path in paths:
        if is_debug == True:
            # 绘制每个填充区域的边界
            print(&#34;{%d,%d,%d,%d}&#34; % (start_x, start_y, end_x, end_y))
            draw.rectangle((start_x&#43;1, start_y&#43;1, end_x-1, end_y-1), outline = get_random_color())

            # 居中绘制填充的图片索引
            index_str = &#34;%d&#34;%(index)
            font_size_x, font_size_y = draw.textsize(index_str)
            draw.text((start_x &#43; (x-font_size_x)/2, start_y &#43; (y-font_size_y)/2), index_str, fill=&#39;black&#39;)
        else:
            src_image = Image.open(path)
            start_x0 = start_x &#43; (x-image_width)//2
            start_y0 = start_y &#43; (y-image_height)//2
            end_x0 = start_x0 &#43; image_width
            end_y0 = start_y0 &#43; image_height
            full_image.paste(src_image, (start_x0, start_y0, end_x0, end_y0), src_image)

        index = index &#43; 1
        if end_x &gt;= full_x:
            # 从左往右填充，遇到边界之后切换到下一行
            start_x = 0
            end_x = start_x &#43; x

            start_y = start_y &#43; y
            end_y = start_y &#43; y
        else:
            # 继续从左往右平铺
            start_x = start_x &#43; x
            end_x = end_x &#43; x

    current_dir = os.getcwd()
    output_path = &#39;%s/../multi_line_%d.png&#39; % (current_dir, merge_times)
    full_image.save(output_path)
```

---

附件：[char_image_merge_tool.zip](/assets/2021-04-25/char_image_merge_tool.zip)

---

> 作者: [YaoXuanZhi](https://github.com/YaoXuanZhi)  
> URL: http://localhost:54598/posts/char_image_merge/  

