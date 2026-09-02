# HTML

## 1. HTML概述

### 1.1 什么是HTML

HTML是Hyper Markup Language的简称，即超文本标记语言，是一种用于创建网页的标准标记语言。

HTML运行在浏览器上，由浏览器来解析。

### 1.2 HTML基本结构

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>凌晨一点的猫</title>
    </head>
    <body>
        
    </body>
</html>
```

<font color = "blue">基本结构说明</font>

`<!DOCTYPE html>`表示定义的文档类型为 HTML5 文档

`<html></html>`表示整个 HTML 文档内容的定义只能在该标签对之间

`<head></head>`表示整个 HTML 文档的头部信息，比如文档的标题、文档使用的字符集编码、文档是否可以缩放等

`<meta charset="utf-8">` 表示定义文档的字符集编码为"uft-8"，支持中文

`<title></title>`表示定义文档显示的标题

`<body></body>`表示 HTML 文档的主体内容部分应该定义在标签内

<font color = "red">**标签一般都是成对出现，分贝叫开放标签和闭合标签** </font>



## 2. HTML 标签

HTML 标签分为两大类：块级标签（block elements）和行级标签（inline-block elements）

### 2.1 块级标签

#### 2.1.1 块级标签特征

1. 总是在新行上开始
2. 高度，行高以及外边距和内边距都可控制
3. 宽度缺省是它的容器的100%
4. 可以容纳内联元素和其他块元素

#### 2.1.2 标题标签

```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

#### 2.1.3 水平线标签

```html
<hr />
```

#### 2.1.4 段落标签

```html
<h1>段落标签</h1>
<hr />
<p>
    段落内容
</p>
```

#### 2.1.5 无序列表标签

```html
<!--Unordered List 无序列表-->
<ul>
    <!--List Item 列表项-->
    <li>列表内容</li>
    <li>列表内容</li>
    <li>列表内容</li>
    <li>列表内容</li>
</ul>
```

#### 2.1.6 有序列表

```html
<!--Ordered List 有序列表   type表示序号的类型，可以调整默认为1-->
<ol type="A">
    <!--List Item 列表项-->
    <li>列表内容</li>
    <li>列表内容</li>
    <li>列表内容</li>
    <li>列表内容</li>
</ol>
```

#### 2.1.7 表格标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>凌晨一点的猫</title>
</head>
<body>
    <!--表格的每一个元素都有边框-->
    <table border="1">
        <!--表格的标题-->
        <caption><h1>薪资表</h1></caption>
        <!--表头-->
        <thead>
            <!--table row-->
            <tr>
                <td>姓名</td>
                <td>薪资</td>
            </tr>
        </thead>
        <!--表格的主体部分-->
        <tbody>
            <tr>
                <td>张三</td>
                <td>20000</td>
            </tr>
            <tr>
                <td>李四</td>
                <td>15000</td>
            </tr>
        </tbody>
        <!--脚注：主要用于信息统计-->
        <tfoot>
            <tr>
                <td>平均薪资</td>
                <td>175000</td>
            </tr>
            <tr>
                <td>总薪资</td>
                <td>35000</td>
            </tr>
        </tfoot>
    </table>


    <!--不规则表格-->
    <table border="1">
        <caption><h1>成绩信息表</h1></caption>
        <thead>
        <tr>
            <!--rowspan 行的范围，2表示占2行，默认值是1-->
            <!--colspan 列的范围，1表示占1列，默认值是1-->
            <td rowspan="2" colspan="1">姓名</td>
            <td rowspan="1" colspan="5">成绩</td>
        </tr>
        <tr>
            <td rowspan="1" colspan="1">6月</td>
            <td rowspan="1" colspan="1">7月</td>
            <td rowspan="1" colspan="1">8月</td>
            <td rowspan="1" colspan="1">9月</td>
            <td rowspan="1" colspan="1">10月</td>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td>张三</td>
            <td>60</td>
            <td>70</td>
            <td>80</td>
            <td>90</td>
            <td>65</td>
        </tr>
        </tbody>
    </table>
</body>
</html>
```

#### 2.1.8 层标签

```html
<div>
    内容
</div>
```

> 段落标签和层标签的区别：
>
> 1. **语义化 (Semantics)**
>
> - **`<p>` (Paragraph)**：**强语义标签**。它明确告诉浏览器、搜索引擎（SEO）和屏幕阅读器，其包含的内容是一段文本。
> - **`<div>` (Division)**：**无语义标签**。它纯粹是一个通用的结构化容器，用于将不同的内容分组、划分页面区块，以便于通过 CSS 布局或通过 JavaScript 进行操作。
>
> 2. **默认样式 (Default Styles)**
>
> - **`<p>`**：浏览器默认会为 `<p>` 标签添加**上下外边距（margin）**（通常为 `1em`），以确保段落之间有自然的阅读间距。
> - **`<div>`**：浏览器**没有任何默认的内外边距**（margin 和 padding 为 0），除非通过 CSS 手动指定。

#### 2.1.9 表单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>凌晨一点的猫</title>
</head>
<body>
    <!--表单主要用于采集数据，然后发送数据-->
    <form action="first.html" method="get">
        <!--按钮-->
        <input type="submit" value="提交">
    </form>
</body>
</html>
```



### 2.2 行级标签

#### 2.2.1 行级标签特征

1. 和其他元素都在一行上
2. 高度，行高及外边距和内边距不可改变
3. 宽度就是其内容的宽度，不可改变

#### 2.2.2 图像标签

```html
<img src="logo.png" title="鼠标放在上面显示的内容" alt="图片未加载时显示">
```

#### 2.2.3 范围标签

```html
<span>内容</span>
```

#### 2.2.4 超链接标签

```html
<a href="资源地址" target="目标窗口位置">内容</a>
```

其中target常用如下：

**_blank**：在新窗口打开

**_self**：在当前窗口打开，是超链接target属性的默认值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>凌晨一点的猫</title>
</head>
<body>
    <img src="image/image1.jpg" title="凌晨一点的猫" alt="未加载">
    <!--屏幕分辨率：1600 * 900 指的就是像素 pixel-->
    这是一段<span style="font-size: 30px; color: red">难以忘怀</span>的恋情
    <a href="first.html" target="_blank">去第一个页面</a>
</body>
</html>
```

超链接通常分为页面间链接、锚链接和功能性链接

**页面间链接**：

```html
<a href="页面名称">内容</a>
```

**锚链接**：

```html
<a href="页面名称#元素的ID属性值">内容</a>
```

锚链接定位同一个页面中的元素时，页面名称可以省略

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>凌晨一点的猫</title>
</head>
<body>
    <img id="img" src="image/image1.jpg" title="凌晨一点的猫" alt="未加载">
    <!--屏幕分辨率：1600 * 900 指的就是像素 pixel-->
    这是一段<span style="font-size: 30px; color: red">难以忘怀</span>的恋情
    <a href="#a" target="_self">去底部</a>
    <div style="height: 10000px; background-color: black">

    </div>
    <!--id = identity 唯一-->
    <p id="a">
        这是一个段落
    </p>
    <a href="#img">返回顶部</a>
</body>
</html>
```

**功能性链接**：

```html
<a href="mailTo:xxx@qq,com">联系我们</a>
```

#### 2.2.5 输入标签

```html
<input type="类型" name="名称" value="值">
```

| 属性      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| type      | 指定元素的类型，text,passwor, checkbox, radio, submit, reset, file, hidden, iamge, hutton, number, date, datetime-local, 默认为text |
| name      | 指定表单元的名称                                             |
| value     | 元素的初始值。type为radio时必须指定一个值                    |
| size      | 指定表单元的初始宽度。当type为text或password时，表单元素的大小以字符为单位。对于其他类型，宽度以像素为单位 |
| maxlength | type为text或者password时，输入的最大字符数                   |
| checked   | type为radio或checkbox时，指定按钮是否被选中                  |

<font color = "blue">示例</font>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>凌晨一点的猫</title>
</head>
<body>
    <form action="" method="get">
        <input type="text" name="username">
        <input type="password" name="password">
        <input type="number" name="age">
        <input type="date" name="birthday">
        <input type="datetime-local" name="birthday">
        <!-- name属性除了具有采集数据的功能之外，还具有分组的功能-->
        <!-- 这里的name=sex表示这三个单选按钮之间属于同一个组sex，同一个组内的单选按钮只能同时选一个-->
        <!-- 需要注意的是：如果input标签type为radio或checkbox时，
            只要标签上存在checked属性，那么该标签就会出现选中状态-->
        <!-- value 属性的核心作用是：定义表单提交时，发送给服务器的真实数据。
            在标准的 HTML 表单数据交互中，数据是以键值对（Key-Value Pair）的形式进行传输的：
            name 属性充当数据的键（Key）。
            value 属性充当数据的值（Value）-->
        <input type="radio" value="M" name="sex" checked>男
        <input type="radio" value="F" name="sex">女
        <input type="radio" value="C" name="sex">其他
        <!--多选-->
        <input type="checkbox" value="1" name="channel" checked>报纸
        <input type="checkbox" value="2" name="channel">杂志
        <input type="checkbox" value="3" name="channel">网络
        <input type="file" name="head">
        <input type="hidden" name="名称">
        <!--当input的type为submit时，按钮可以提交表单，提交的表单
            通过name属性采集数据-->
        <input type="submit" value="按钮">
    </form>
</body>
</html>
```

#### 2.2.6 文本域

```html
<textarea name="名称" placeholder="提示信息"></textarea>
```

<font color = "blue">示例</font>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <!-- label标签和span标签都具有范围选择的功能 -->
    <label>这是标题</label>
    <label>
        <!--文本域-->
        <textarea rows="10" cols="50"></textarea>
    </label>

    <form action="" method="">
        <div>
            <!-- for属性一定是一个input输入框的ID值
                表示label标签被点击时，对应的input输入框获得焦点 -->
            <label for="input1">账号：</label>
            <input id=input1 type="text" name="username">
        </div>
    </form>
</body>
</html>
```

#### 2.2.7 下拉列表

```html
<select>
    <option value="值">显示值</option>
    <option value="值">显示值</option>
    <option value="值">显示值</option>
    ···
    <option value="值">显示值</option>
</select>
```

<font color = "blue">示例</font>

```html
<div>
    <!--下拉列表-->
    <select name="sex">
        <option value="">请选择</option>
        <option value="M">男</option>
        <option value="F">女</option>
        <option value="O">其他</option>
    </select>
</div>
```

**<font color = "red">只读和禁用</font>**

```html
<!--只读-->
<input type="text" value="admin" readonly>
<!--禁用-->
<input type="text" value="admin" disabled>
```



## 3. 综合应用

![exercise1](image\html-exercise1.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <form action="" method="get">
        <div>
            <label for="admin">账号</label>
            <input id="admin" type="text" name="username">
        </div>
        <div>
            <label for="password">密码</label>
            <input id="password" type="password" name="password">
        </div>
        <div>
            <input type="submit" value="登录">
        </div>
    </form>
</body>
</html>
```



![exercise2](image\html-exercise2.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <form action="" method="get">
        <div>
            <label for="username">账&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;号</label>
            <input id="username" type="text" name="username">
        </div>
        <div>
            <!--&nbsp; 转义字符-->
            <label for="password">密&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;码</label>
            <input id="password" type="password" name="password">
        </div>
        <div>
            <label for="confirmPassword">确认密码</label>
            <input id="confirmPassword" type="password" name="confirmPassword">
        </div>
        <div>
            <label>性&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;别</label>
            <input type="radio" value="M" name="sex" checked>男
            <input type="radio" value="F" name="sex">女
            <input type="radio" value="O" name="sex">其他
        </div>
        <div>
            <label>国&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;籍</label>
                <select name="country">
                    <option value="">请选择</option>
                    <option value="CN">中国</option>
                    <option value="USA">美国</option>
                </select>
        </div>
        <div>
            <input type="submit" value="登录">
        </div>
    </form>
</body>
</html>
```



![exercise3](image\html-exercise3.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <div>
        <span>姓名</span>
        <input type="text" name="name">
        <span>性别</span>
        <select>
            <option value="">请选择</option>
            <option value="M">男</option>
            <option value="F">女</option>
            <option value="O">其他</option>
        </select>
        <input type="button" value="查询">
    </div>

    <table border="1" width="100%">
        <thead>
            <tr>
                <td>姓名</td>
                <td>性别</td>
                <td>年龄</td>
                <td>成绩</td>
            </tr>
        </thead>
        <tbody>
        <tr>
            <td>张三</td>
            <td>男</td>
            <td>20</td>
            <td>80</td>
        </tr>
        <tr>
            <td>李四</td>
            <td>女</td>
            <td>18</td>
            <td>90</td>
        </tr>
        <tr>
            <td>王五</td>
            <td>男</td>
            <td>3</td>
            <td>66</td>
        </tr>
        </tbody>
    </table>
</body>
</html>
```



## 4. HTML5新增元素

### 4.1 结构标签

| 标签    | 说明             |
| ------- | ---------------- |
| header  | 页面页眉         |
| nav     | 页面导航         |
| main    | 页面主要内容区块 |
| section | 页面内容区块     |
| article | 独立的内容块     |
| aside   | 侧边栏           |
| footer  | 页面脚注         |

<font color = "blue">示例</font>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
    <style>
        html,body {
            width: 100%;
            height: 100%;
            margin: 0;
            padding: 0;
        }
        header {
            height: 40px;
            background-color: black;
            color: white;
        }
        main {
            height: calc(100% - 40px);
            display: grid;/*网格布局*/
            grid-template-columns: 200px calc(100% - 200px);
        }
        aside {
            background-color: red;
        }
        section {
            background-color: antiquewhite;
        }
        nav {
            height: 40px;
            background-color: aqua;
        }
        footer {
            height: 40px;
            background-color: black;
            color: white;
        }
        article {
            height: calc(100% - 80px);
        }
    </style>
</head>
<body>
    <header>
        页面页眉
    </header>
    <!--页面主体部分-->
    <main>
        <aside>
            侧边栏
        </aside>
        <!--页面内容区块-->
        <section>
            <nav>操作导航</nav>
            <article>操作导航</article>
            <footer>页面脚注</footer>
        </section>
    </main>
</body>
</html>
```

![结构化标签](image\结构化标签.png)

### 4.2 其他标签

| 标签     | 说明                                 |
| -------- | ------------------------------------ |
| audio    | 定义音频，如音乐或其他音频流         |
| video    | 定义视频，如电影片段或其他视频流     |
| canvas   | 定义图片                             |
| datalist | 定义可选数据的列表                   |
| time     | 标签定义日期或时间                   |
| mark     | 在视觉上向用户呈现那些需要突出的文字 |

#### 4.2.1 音频标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <!--controls属性控制页面上是否显示音频的操作控件
        autoplay属性表示音频在就绪后马上播放
        loop属性表示音频结束后重新播放
        preload的值：
        auto - 当页面加载后载入整个音频
        metadata - 当页面加载后只载入数据元
        none - 当页面加载后不载音频-->
    <audio src="音频路径" controls="controls" autoplay="autoplay" preload="auto" loop="loop"></audio>
</body>
</html>
```

常见的音频格式：MP3、OGG、Wav

音频还支持多个音频文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <!--controls属性控制页面上是否显示音频的操作控件
        autoplay属性表示音频在就绪后马上播放
        loop属性表示音频结束后重新播放
        preload的值：
        auto - 当页面加载后载入整个音频
        metadata - 当页面加载后只载入数据元
        none - 当页面加载后不载音频-->
    <audio controls="controls" autoplay="autoplay" preload="auto" loop="loop">
        <source src="音频路径.mp3" />
        <source src="音频路径.OGG" />
        <source src="音频路径.wav" />
        浏览器不支持该音频格式
    </audio>
</body>
</html>
```

#### 4.2.2视频标签

```html
<video src="" controls="controls" autoplay="autoplay" preload="auto"></video>
```

常见的视频格式：avi、flv、mp4、mkv、ogv

视频标签的用法与audio标签一样

#### 4.2.3 列表标签

```html
<input list="id">
<datalist id="id">
    <option>男</option>
    <option>女</option>
    <option>其他</option>
</datalist>
```

#### 4.2.4 时间与标记标签

```html
<p>
    <!--时间标签没有什么实际意义，只是供机器识别：比如搜索引擎、爬虫分析-->
    我在<time datetime="2026-5-20">520</time>有个约会
</p>
```

```html
<p>
    她长得很<mark>漂亮</mark>
</p>
```



## 5. HTML新增元素属性

### 5.1 全局属性

| 属性             | 说明                             |
| ---------------- | -------------------------------- |
| contentdeditable | 元素是否允许可编辑内容           |
| spellcheck       | 是否必须对元素进行拼写或语法检查 |
| tabindex         | 指定元素的tab键选择次序          |

<font color = "blue">示例</font>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>lullaby</title>
</head>
<body>
    <div style="border: 1px solid #ddd; height: 200px" contenteditable="true" spellcheck="true" tabindex="2"></div>
    <div style="border: 1px solid red; height: 200px" contenteditable="true" spellcheck="true" tabindex="3"></div>
    <div style="border: 1px solid blue; height: 200px" contenteditable="true" spellcheck="true" tabindex="1"></div>
</body>
</html>
```

### 5.2 表单属性

| 属性        | 说明                               |
| ----------- | ---------------------------------- |
| placeholder | 指定元素的默认提示信息             |
| require     | 元素内容为必填                     |
| pattern     | 使用正则表达式检查元素内容是否合法 |

