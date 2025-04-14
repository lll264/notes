# 推荐文章

https://blog.csdn.net/qq_52914337/article/details/124425239

# HTML

内容很简单，直接看菜鸟文档

## 特殊字符

在 HTML 页面中，一些特殊的符号很难或者不方便直接使用，此时我们就可以使用下面的字符来替代。

![image-20250404182706040](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404182706040.png)

重点记住：空格 、大于号、 小于号 这三个， 其余的使用很少，如果需要回头查阅即可。

## 基本标签

```html
<!DOCTYPE html>
 
<html lang="zh-cn">
 
<head>
<meta charset="utf-8">
<title>HTML基本标签</title>
</head>
 
<body>
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
 
<p>这是一个段落。</p>
<br> <!--换行-->
<hr> <!--水平线-->
<!-- 注释 -->
</body>
 
</html>
```

### 标题
```html
<h1>：一级标题

<h2>：二级标题

<h3>：三级标题

<h4>：四级标题

<h5>：五级标题

<h6>：六级标题
```
### 段落
```html
<p>（paragraph）：可以将文档分割为若干段落。
```

### 注释
```html
<!-- 注释 -->可以将注释插入 HTML 代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。
```
### 水平线

```html
<hr/>：可用于分隔内容。
```
属性：

- color：设置水平线的颜色。
- width：设置水平线的长度。
- size：设置水平线的高度。

### 换行符

```html
<br/>：可用于换行。
```



## 文本格式化

### 示例

![image-20250401231725251](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250401231725251.png)

```html
<!DOCTYPE html>

<html lang="zh-cn">

<head>
    <meta charset="utf-8">
    <title>HTML文本格式化</title>
</head>

<body>
    <b>粗体文本</b>
    <br>
    <i>斜体文本</i>
    <br>
    <strong>加重语气</strong>
    <br>
    <em>着重文字</em>
    <br>
    <big>大号字体</big>
    <br>
    <small>小号字体</small>
    <br>
    <sub>下标</sub>
    <br>
    <sup>上标</sup>
    <br>
    <ins>插入字</ins>
    <br>
    <del>删除字</del>
</body>

</html>
```
### 解析

```html
<b>（blod）：粗体文本

<i>（italic）：斜体文本

<strong>：加重语气

<em>（emphasized）：着重文字

<big>：大号字体

<small>：小号字体

<sub>（subscripted）：下标

<sup>（superscripted）：上标

<ins>（inserted）：插入字

<del>（deleted）：删除字
```



## 链接

```html
<!DOCTYPE html>

<html lang="zh-cn">

<head>
    <meta charset="utf-8">
    <title>HTML链接</title>
</head>

<body>
    <a name = "top">顶部</a>
    <a href="https://www.example.com/" target="_blank" rel="noopener noreferrer" title="示例域">示例域</a>
    <a href="示例文件.md" download="指定文件名.md">下载文件</a>
    <a href="mailto:3466097182@qq.com">点击联系我</a>
    <a href="#top">回到顶部</a>
</body>

</html>
        HTML使用标签 <a> 来设置超文本链接。
```

### 页面间链接

属性：

- **href（hypertext reference）**：定义链接目标。

- **target**：定义链接的打开方式。    

  - _blank：在新窗口或新标签页中打开链接。

  - _self：在当前窗口或标签页中打开链接（默认）。

  - _parent： 在父框架中打开链接.

  - _top：在整个窗口中打开链接，取消任何框架。

- **rel（reload）**：定义链接与目标页面的关系。
  - nofollow: 表示搜索引擎不应跟踪该链接，常用于外部链接。
  - noopener 和 noreferrer: 防止在新标签中打开链接时的安全问题，尤其是使用 target="_blank" 时。
    download：提示浏览器下载链接目标而不是导航到该目标。
- **title**：定义链接的额外信息，当鼠标悬停在链接上时显示的工具提示。

### 锚链接

需要一个锚标记：name="(你自己取的名字)"
跳转到标记：href="#(你自己取的名字)"

### 功能性链接

邮箱链接：href=" mailto:(这里写邮箱地址)"

## 图像

```html
<!DOCTYPE html>

<html lang="zh-cn">

<head>
    <meta charset="utf-8">
    <title>HTML图像</title>
</head>

<body>
    <img src="淡淡百合香，芬芳绕心间.jpg" alt="我们白河豚最爱看的图" width="400px" height="280px" title="看什么看！">
    <img src="url" alt="我们白河豚最爱看的图" width="400px" height="280px" title="看不到吧，因为我根本就没放">
</body>

</html>
        
```
在 HTML 中，图像由 <img> 标签定义。<img>是空标签，意思是说，它只包含属性，并且没有闭合标签。

**属性：**

- src（source）：源属性的值是图像的 URL 地址。

- alt（alternative）：用来为图像定义一串预备的可替换的文本。

- width：设置图像的高度。

- height：设置图像的宽度。

- title：把鼠标悬停在图片上有提示。


**图片路径详解**

1.绝对路径

​    绝对路径是电脑的盘符存储和访问的具体地址

2.相对路径

子级关系：/
父级关系：../
同级关系：./

3.网络路径

​    具体的网络地址

## 视频

### 示例

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>媒体</title>
</head>

<body>
    <video src="/视频.mp4" width="1280px" height="720px" controls autoplay></video>
    <audio src="/音频.mp3" controls autoplay></audio>
</body>

</html>
```
### 视频

```html
<video> 元素定义一个视频。
```
属性：

- src（source）：视频资源路径。

- width：设置视频的高度。

- height：设置视频的宽度。

- controls：供添加播放、暂停和音量控件。

- autoplay：自动播放。


### 音频

```html
<audio> 元素定义了声音内容。
```
属性：

- src（source）：视频资源路径。

- controls：供添加播放、暂停和音量控件。

- autoplay：自动播放。

## 列表

### 示例

```html
<!DOCTYPE html>

<html lang="zh-cn">

<head>
    <meta charset="utf-8">
    <title>HTML列表</title>
</head>
<body>
    <!--有序列表-->
    <ol type="1">
        <li>桃子</li>
        <li>葡萄</li>
        <ol>
            <li>巨峰葡萄</li>
            <li>阳光玫瑰葡萄</li>
        </ol>
    </ol>

```html
<ol type="A">
    <li>桃子</li>
    <li>葡萄</li>
</ol>
 
<ol type="a">
    <li>桃子</li>
    <li>葡萄</li>
</ol>
 
<ol type="I">
    <li>桃子</li>
    <li>葡萄</li>
</ol>
 
<ol type="i">
    <li>桃子</li>
    <li>葡萄</li>
</ol>

<hr>
 
<!--无序列表-->
<ul type="disc">
    <li>桃子</li>
    <li>葡萄</li>
</ul>
 
<ul type="circle">
    <li>桃子</li>
    <li>葡萄</li>
</ul>
 
<ul type="square">
    <li>桃子</li>
    <li>葡萄</li>
</ul>
 
<ul type="none">
    <li>桃子</li>
    <li>葡萄</li>
</ul>

<hr>
 
<!--自定义列表-->
<dl>
    <dt>葡萄</dt>
    <dd>好吃</dd>
</dl>
```
![d0d8b842b41e4f5c87b00b17e25b20dc.png](D:\E\学习\Go\笔记\技术栈\前端\assets\d0d8b842b41e4f5c87b00b17e25b20dc.png)

### 有序列表

```html
<ol>（ordered list）：定义有序列表
<li >（list item）：定义列表项
```
type属性：

- 1：表示列表项目用数字标号（1,2,3…）
- A：表示列表项目用大写字母标号（A,B,C…）
- a：表示列表项目用小写字母标号（a,b,c…）
- I：表示列表项目用大写罗马数字标号（I,II,III…）
- i：表示列表项目用小写罗马数字标号（i,ii,iii…）

### 无序列表
```html
<ul>（unordered list）：定义无序列表
<li>（list item）：定义列表项
```
type属性：

- disc：实心圆
- circle：空心圆
- square：小方块
- none：不显示

### 自定义列表
```html
<dl>（definition list）：定义列表
<dt>（definition trem）：自定义列表项目
<dd>（definition discription）：定义自定列表项的描述
```

## 表格

### 示例

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250401225937971.png" alt="image-20250401225937971"  />

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>表格</title>
</head>

<body>

    <table border="1px">
     
        <thead>
     
            <tr>
                <th>列标题1</th>
                <th>列标题2</th>
                <th>列标题3</th>
            </tr>
     
        </thead>
     
        <tbody>
            <tr>
                <td colspan="2">跨列</td>
                <td rowspan="2">跨行</td>
            </tr>
     
            <tr>
                <td>第二行第一列</td>
                <td>第二行第二列</td>
            </tr>
     
        </tbody>
     
    </table>

</body>

</html>
```

### 解析

```html
<table>：整个表格。
<thead>：定义表格的标题部分。
<tbody>：定义表格的主体部分。
<tr>（table row）：表格的一行。
<td>（table data）：表格的数据单元格。
<th>（table header）：表格的表头单元格。
```
属性：

- border：边框。

- colspan：可用于跨列。

- rowspan：可用于跨行。

### 合并单元格

![在这里插入图片描述](D:\E\学习\Go\笔记\技术栈\前端\assets\9f24fc1cde815d4ccbdf315d14d468eb.png)

```html

1.跨列合并第一行的第二列和第三列
            <tr>
                <tr>
                <td></td>
                <td colspan="2"></td><!--根据要求，找到第一行第二列，写上属性colspan,并且写上参数2，代表合并第二列和第三列，与此同时，注释掉第一行第三列-->
                <!-- <td></td> -->
            </tr>
2.跨行合并第一列的第二行和第三行
		   <tr>
                <td rowspan="2"></td><!--根据要求，找到第一列第二行，写上属性rowspan,并且写上参数2，代表合并第二行和第三行，与此同时，注释掉第一列第三行-->
                <td></td>
                <td></td>
            </tr>
            <tr>
                <!-- <td></td> -->
                <td></td>
                <td></td>
            </tr>

```



## 表单

### 示例

![image-20250401230654438](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250401230654438.png)

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>表单</title>
</head>

<body>

    <form>
        <label for="username">用户名：</label>
        <input type="text" id="username" placeholder="请输入用户名">
        <br>
        <br>
     
        <label for="pwd">密码：</label>
        <input type="password" id="pwd" placeholder="请输入密码">
        <br>
        <br>
     
        <label>性别：</label>
        <input type="radio" name="gender">男
        <input type="radio" name="gender">女
        <input type="radio" name="gender">其它
        <br>
        <br>
     
        <label>爱好：</label>
        <input type="checkbox" name="hobby">唱歌
        <input type="checkbox" name="hobby">跳舞
        <input type="checkbox" name="hobby">rap
        <input type="checkbox" name="hobby">篮球
        <br>
        <br>
     
        <input type="submit" value="上传">
        <br>
        <br>
        
        <select name="列表名称">
            <option value="1">选择1</option>
            <option value="2">选择2</option>
            <option value="3">选择3</option>
            <option value="4">选择4</option>
        </select>
        <br>
        <br>
     
        <label for="text1">文本：</label>
        <textarea name="文本域" id="text1" rows="10" cols="50">文本内容</textarea>
        <br>
        <br>
     
        <label for="files"></label>
        <input type="file" name="files" id="files">
        <br>
        <br>
     
        <label for="num">数字：</label>
        <input type="number" name="num" id="num" max="100" min="10" step="10">
        <br>
        <br>
        
        <label for="voice">音量：</label>
        <input type="range" name="voice" id="voice" min="0" max="100" step="2">
        <br>
        <br>
     
        <label for="text1">搜索：</label>
        <input type="search" name="search" id="search">
    
    	<select>
        <option>山东</option>
        <option>北京</option>
        <option>天津</option>
        <option selected="selected">火星</option>
    	</select>
        
        今日反馈:
        <textarea cols="50" rows="5">我知道这个反馈留言是textarea来做的 </textarea>
    </form>

</body>

 

</html>
```

### form

用于创建表单。

属性：

- action：定义了表单数据提交的目标 URL。

- method：定义了提交数据的 HTTP 方法。


### label

用于为表单元素添加标签，提高可访问性。

label 标签可以通过id绑定一个表单元素, 当点击<label> 标签内的文本时，浏览器就会自动将焦点(光标)转到或者选择对应的表单元素上,用来增加用户体验.

### input

可以创建文本输入框、密码框、单选按钮、复选框等。

**属性：**

- type：输入类型。

  - 文本域 text（text fields）：当用户要在表单中键入字母、数字等内容时，就会用到文本域。
  
  
    - 密码字段 password：定义了表单的密码框。
  
  
    - 单选按钮 radio（radio buttons）：定义了表单的单选框选项。
  
  
    - 复选框 checkbox（checkboxes）：定义了复选框。
  
  
    - 普通按钮 button：定义了普通按钮。
  
  
    - 提交按钮 submit：定义了提交按钮。
  
  
    - 图像按钮 image：定义了图像按钮。
  
  
    - 文件域 file：元素允许用户可以从他们的设备中选择一个或多个文件。
  
  
    - 数字 number：用于输入数字的字段，以及如何设置最大值、最小值、间隔和默认值。
  
  
    - 滑块 range：允许用户指定一个数值，该数值必须不小于给定值，并且不得大于另一个给定值。
  
  
    - 搜索框 search：专为用户输入查询文本而设计的字段。
    - 提交按钮 submit，通过value指定按钮文字
    - 重置按钮 reset，通过value指定按钮文字
  


- id：用于关联<label>元素。

- name：表单元素的名称。

- value：默认初始值。

- placeholder：type为text或password时，定义了当表单控件没有值时在控件中显示的文本。占位符文本应简要提示用户应向控件输入的预期数据类型。

- maxlength：type为text或password时，最长能写几个字符。

- checked：type为radio或checkbox时，指定按钮是否被选中。

- readonly：元素是不可变的，意味着用户无法编辑控件。

- hidden：允许 Web 开发者包含用户不可见、不可改的数据，在用户提交表单时，这些数据会一并发送出。

- disabled：既不可用，也不可点击。

- required：用户在提交输入所属的表单前必须为其指定一个值。


### selest

用于创建下拉列表。

**属性：**

- id：用于关联<label>元素。

- name：下拉列表名称。


### option

用于定义下拉列表中的选项。

**属性：**

- value：名称的值。

- selected：规定在页面加载时应预先选择一个选项。

### textarea

文本域。

**属性：**

- value：名称的值。

- placeholder：定义了当表单控件没有值时在控件中显示的文本。占位符文本应简要提示用户应向控件输入的预期数据类型。

- selected：规定在页面加载时应预先选择一个选项。

- rows：文本的行。

- cols：文本的列。


## 框架

### 示例

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>框架</title>
</head>

<body>
    <iframe src="" name="frame"  width="1260px" height="720px"></iframe>
    <a href="https://mp.csdn.net/" target="frame">点击跳转</a>
</body>

</html>
```
iframe 标签规定一个内联框架。一个内联框架被用来在当前 HTML 文档中嵌入另一个文档。

**属性：**

- src（source）：资源路径。

- name：框架标识名。

- width：设置视频的高度。

- height：设置视频的宽度。

# CSS

## 简介

CSS 是**层叠样式表 ( Cascading Style Sheets )** 的简称.有时我们也会称之为 CSS 样式表或级联样式表。CSS 是也是一种**标记语言**。

CSS 主要用于设置 HTML 页面中的文本内容（字体、大小、对齐方式等）、图片的外形（宽高、边框样式、边距等）以及版面的布局和外观显示样式。

CSS 让我们的网页更加丰富多彩，布局更加灵活自如。简单理解：CSS 可以美化 HTML , 让 HTML 更漂亮，让页面布局更简单。

## 引入方式

### 内部样式表

也称为内嵌样式，在页面头部通过[style标签](https://so.csdn.net/so/search?q=style标签&spm=1001.2101.3001.7020)定义对当前页面中所有符合样式选择器的标签都起作用

```html
<style>
  div {
	color: red;
	font-size: 12px;
  }
</style>
```

### 行内样式表

也称为嵌入样式，使用HTML标签的style属性定义，只对设置style属性的标签起作用

### 外部样式表

使用单独的 `.CSS` 文件定义，然后在页面中使用 `link标签` 或 `@import指令` 引入

```html
<link rel="stylesheet" type="text/css" href="CSS样式文件的路径">
```

```html
<style>
	@import "CSS样式文件路径";
	@import url(CSS样式文件路径);
</style>
```

## 选择器

### 简介

CSS就是通过定义并使用选择器来该变网页元素的样式的。选择器基本分为两大类。

### 基础选择器

![image-20250402120228038](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402120228038.png)

#### 示例

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
        标签选择器
		p{ 
			color:red;
			font-size:20px;
		}
		h2{
			color:yellow;
		}
        类选择器
		.hello{
			background: #cccccc;
		}
		.world{
			font-weight:bold;
		}
        ID选择器
		#haha{
			color:blue;
		}
	</style>
</head>
<body>
	<p>welcome to css!</p>
	<p>hello world!</p>
	<h2>WEB前端开发</h2>
	<h3>Java开发</h3>
	<hr>
	<p class="hello">welcome to css!</p>
	<p>hello world!</p>
	<h2>WEB前端开发</h2>
	<h3>Java开发</h3>
	<div class="hello">主讲：Hector</div>
	<div class="world">主讲：Hector</div>
	<hr>
	<h1 id="haha">哈哈</h1>
</body>
</html>

```

#### 标签选择器

```html
标签名{
属性1: 属性值1;
属性2: 属性值2;
属性3: 属性值3;
...
}
```

也称为元素选择器，使用HTML标签作为选择器的名称

以标签名作为样式应用的依据

#### 类选择器

```
.类名 {
属性1: 属性值1;
...
}
```

使用自定义的名称，以 . 号作为前缀，然后再通过HTML标签的class属性调用类选择器

以标签的class属性作为样式应用的依据

**注意事项：**

调用时不能添加 . 号
同时调用多个类选择器时，以 空格 分隔
类选择器名称不能以 数字 开头

#### ID选择器

```
#id名 {
属性1: 属性值1;
...
}
```

使用自定义名称，以 `#` 作为前缀，然后通过HTML标签的id属性进行名称匹配

以标签的id属性作为样式应用的依据，一对一的关系

#### 总结表格

![image-20250402183110906](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402183110906.png)

### 复合选择器

#### 总结表格



#### 嵌套选择器

嵌套选择器又称为包含选择器，在某个选择器内部再设置选择器，通过空格隔开。可以**选择父元素里面子元素，改变其样式**。其写法就是把外层选择器写在前面，内层选择器写在后面，层层嵌套，中间用空格分隔。

```html
元素1 元素2 ... { 样式声明 }

ul li {}
div h1.class1 {}
ui li a {}
```

- 元素1 和 元素2 中间用空格隔开
- 元素1 是父级，元素2 是子级，最终选择的是元素2
- **元素2 可以是儿子，也可以是孙子等，只要是元素1 的后代即可**

```html
<style>
  .hot a {
    color: red;
  } 
</style>
   
<div class="hot">
  <a href="#">大肘子</a>
   <ul>
     <li><a href="#">猪头</a></li>
     <li><a href="#">猪尾巴</a></li>
   </ul>
</div>
```

.hot a会影响到.hot样式下的所有a，不管嵌套多少级

- **元素1 和 元素2 可以是任意基础选择器**

```html
<ul class="gg">
  <li>1</li>
  <li>2</li>
</ul>

嵌套方式改变li的样式有两种方式
1.
ul li {}
2.
.gg li {}
```

#### 子选择器

只有满足层次关系最里层的选择器所对应的标签才会应用样式

```
元素1 > 元素2 { 样式声明 }

div > p {}
```

- 元素1 和 元素2 中间用 大于号 隔开
- 元素1 是父级，元素2 是子级，最终选择的是元素2
- **元素2 必须是亲儿子**，其孙子、重孙之类都不归他管. 你也可以叫他 亲儿子选择器

#### 并集选择器

并集选择器可以选择多组标签, 同时为他们定义相同的样式。通常用于集体声明.

```
元素1,元素2 { 样式声明 }
```

- 元素1 和 元素2 中间用逗号隔开
- 逗号可以理解为和的意思
- 并集选择器通常用于集体声明

#### 伪类选择器

伪类选择器用于向某些选择器添加特殊的效果，**比如给链接添加特殊效果**，或选择第1个，第n个元素。**伪类选择器书写最大的特点是用冒号（:）表示**，比如 :hover 、 :first-child 。

因为伪类选择器很多，比如有链接伪类、结构伪类等，所以这里先给大家讲解常用的链接伪类选择器。链接四种状态：

- a:link 未访问的链接
- a:visited 已访问的链接
- a:hover 鼠标悬浮到连接上，即移动在连接上
- a:active 选定的链接，被激活

注：默认超链接为：蓝色、下划线

除了链接伪类，:focus 伪类选择器用于选取获得焦点的表单元素。焦点就是光标，一般情况 input 类表单元素才能获取，因此这个选择器也主要针对于表单元素来说。

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>伪类选择器</title>
	<style>
		/*a:link{
			font-size: 12px;
			color:black;
			text-decoration: none;
		}
		a:visited{
			font-size: 15px;
			color:;
		}
		a:hover{
			font-size: 20px;
			color:blue;
		}
		a:active{
			font-size: 40px;
			color:green;
		}*/
		a:link,a:visited{
			color:#666666;
			font-size: 13px;
			text-decoration: none;
		}
		a:hover,a:active{
			color:#ff7300;
			text-decoration: underline;
		}
		/*普通的标签也可以使用伪类选择器*/
		p:hover{
			color:red;
		}
		p:active{
			color:blue;
		}
        /*记住一种特殊用法*/
        input:focus {
			background-color:yellow;
		}
	</style>
</head>
<body>
	<a href="复杂选择器.html">复杂选择器.html</a>
	<p>CSS从入门到精通！</p>
</body>
</html>

```

### 选择器优先级

行内样式>ID选择器>类选择器>标签选择器

原因：首先加载标签选择器,再加载类选择器，然后加载ID选择器，最后加载行内样式

后加载会覆盖先加载的同名样式

## 常用CSS属性

### 字体属性

设置字体相关的样式

#### 总结表格

![image-20250402183439143](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402183439143.png)

#### font-family

要求系统中要安装指定的字体

一般建议写3种字体：首选、其次、备用。以逗号隔开

#### font-size

多数浏览器默认为16px

**取值：**

- inherited继承，默认从父标签继承字体大小（默认值），所有CSS属性的默认值都是inherited
- px像素 pixel
- %百分比，相对父标签字体大小的百分比
- em倍数，相对于父标签字体大小的倍数
- HTML根元素默认字体的大小为16px,也称为基础字体大小

#### font-weight

**取值：**

- normal普通（默认）

- bold粗体
- 自定义400 normal 700 bold

#### font-style

**取值：**

- normal普通

- italic斜体

#### font复合属性

简写属性：

```html
p {
  font:font-style font-weight font-size/line-height font-family
}
```

必须按此顺序书写

### 文本属性

#### 总结表格

![image-20250402183924326](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402183924326.png)

#### color

color取值，四种写法：

- 颜色名称：使用英文单词

- 16进制的RGB值：#RRGGBB

- 特定情况下可以缩写.


```
#FFFFFF--->#FFF 白色 
#000000--->#000 黑色 
#FF0000--->#F00 红色 
#00FF00--->#0F0 绿色 
#0000FF--->#00F 蓝色 
#CCCCCC--->#CCC 灰色 
#FF7300--->无法简写
```

- rgb函数：rgb(red,green,blue)

注意：可以设置透明度，alpha取值范围：[0,1] 0表示完全透明 1表示完全不透明

#### text-align

文本对齐方式：left、center、right

#### text-decoration

![image-20250402184300881](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402184300881.png)

#### text-indent

text-indent 属性用来指定文本的第一行的缩进，通常是将段落的首行缩进。

段落缩进2个字，text-indent=2em

#### line-height

每一行文本内容的高度，而height是单纯的内容的高度。height不会继承，当时行高会继承

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402203452641.png" alt="image-20250402203452641" style="zoom: 80%;" />

#### 文本垂直居中原理

**记住行高等于盒子高度即可**

比如行高30px，上下间距和文本高度都是10px，那么放到40px高度的盒子中就偏上了。如果行高50px，超出了盒子自然就偏下了。

### 背景属性

#### 总结表格

![image-20250402210256415](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402210256415.png)

#### 背景颜色

background-color 属性定义了元素的背景颜色。一般情况下元素背景颜色默认值是 transparent（透明），我们也可以手动指定背景颜色为透明色。

#### 背景图片

```
background-image: url(images/1.png)
```

background-image 属性描述了元素的背景图像。实际开发常见于 logo 或者一些装饰性的小图片或者是超大的背景图片, 优点是非常便于控制位置. (精灵图也是一种运用场景)

![image-20250402204953810](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402204953810.png)

**注意：背景图片后面的地址，千万不要忘记加 URL， 同时里面的路径不要加引号。**

#### 背景平铺

如果需要在 HTML 页面上对背景图像进行平铺，可以使用 background-repeat 属性。

![image-20250402205104824](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402205104824.png)

#### 背景图片位置

利用 background-position 属性可以改变图片在背景中的位置

```
background-position: x  y;
```

```html
div {
    width: 300px;
    height: 300px;
    background-image: url(images/logo.png);
    background-repeat: no-repeat;
    /* background-position: center top; */
    /* background-position: right center; */
    /* 如果是方位名词  right center 和 center right 效果是等价的 跟顺序没有关系 */
    /* background-position: center right; */
    /* 此时 水平一定是靠右侧对齐  第二个参数省略 y 轴是 垂直居中显示的 */
    /* background-position: right; */
    /* 此时 第一个参数一定是 top y轴 顶部对齐   第二个参数省略x  轴是 水平居中显示的 */
    background-position: top;
}
```

参数代表的意思是：x 坐标和 y 坐标。 可以使用 **方位名词** 或者 **精确单位**

![image-20250402205445752](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402205445752.png)

#### 背景图像固定（背景附着）

background-attachment 属性设置背景图像**是否固定或者随着页面的其余部分滚动**

![image-20250402205830656](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402205830656.png)

#### 复合写法

background: 背景颜色 背景图片地址 背景平铺 背景图像滚动 背景图片位置;

```html
background: transparent url(image.jpg) repeat-y  fixed  top ;
```

### 列表属性

### 表格属性

### auto

置为 `auto` 的主要作用是 **自动分配剩余空间**

## 元素显示模式

### 总结表格

![image-20250402194400841](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402194400841.png)

### 块元素

常见的块元素有<h1>~<h6>、<p>、<div>、<ul>、<ol>、<li>等，其中 <div> 标签是最典型的块元素。

**块级元素的特点：**

- 比较霸道，自己**独占一行**。
- 高度，宽度、外边距以及内边距都**可以控制**。
- **盒子宽度默认是容器（父级宽度）的100%。，高度默认是内容高度**
- **是一个容器及盒子，里面可以放行内或者块级元素。**

**注意：**p和h1-h6标签里面不能放块级元素

文字类的元素内不能使用块级元素，p 标签主要用于存放文字，因此 **p 里面不能放块级元素**，特别是不能放div。同理， **h1~h6 等都是文字类块级标签，里面也不能放其他块级元素**

### 行元素

常见的行内元素有 <a>、<strong>、<b>、<em>、<i>、<del>、<s>、<ins>、<u>、<span>等，其中<span> 标签是最典型的行内元素。有的地方也将行内元素称为内联元素。**行内元素基本上就是文本，样式会受常见文本属性影响。**

行内元素的特点：

- 相邻行内元素在一行上，一行可以显示多个。
- 高、宽直接设置是无效的。
-  **默认宽度和高度就是它本身内容的宽高**。
- 行内元素只能容纳文本或其他行内元素。

**注意：**链接里面不能再放链接

### 行级块元素

在行内元素中有几个特殊的标签 —— <img />、<input />、<td>，它们同时具有块元素和行内元素的特点。有些资料称它们为行内块元素。

行内块元素的特点：

- **一行可以有多个，但是他们之间会有空白缝隙**。一行可以显示多个（行内元素特点）。
- 默认宽度就是它本身内容的宽度（行内元素特点）。
- 可以设置高度，行高、外边距以及内边距（块级元素特点）。

### 元素显示模式转换

特殊情况下，我们需要元素模式的转换，简单理解: 一个模式的元素需要另外一种模式的特性。比如想要增加链接 <a> 的触发范围。

- 转换为块元素：display:block;
- 转换为行内元素：display:inline;
- 转换为行内块：display: inline-block;

## CSS的特性

### 层叠性

相同选择器给设置相同的样式，此时一个样式就会覆盖（层叠）另一个冲突的样式。层叠性主要解决样式冲突的问题。

**层叠性原则：**

- 样式冲突，遵循的原则是就近原则，哪个样式离结构近，就执行哪个样式
- 样式不冲突，不会层叠

![image-20250402213415002](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402213415002.png)

### 继承性

#### 浮动继承

#### 样式继承

子标签会继承父标签的某些样式，如文本颜色和字号。简单的理解就是：子承父业

![image-20250402213438912](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402213438912.png)

恰当地使用继承可以简化代码，降低 CSS 样式的复杂性。子元素可以继承父元素的样式（text-，font-，line-这些元素开头的可以继承，以及color属性）

#### 行高的继承性

```html
body {
  font:12px/1.5 Microsoft YaHei；
}
```

行高可以跟单位也可以不跟单位。如果子元素没有设置行高，则会继承父元素的行高为 1.5。此时子元素的行高是：当前子元素的文字大小 * 1.5。body 行高 1.5 这样写法最大的优势就是里面子元素可以根据自己文字大小自动调整行高

### 优先级

![image-20250402213532853](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402213532853.png)

## 盒子模型

所谓 盒子模型：就是把 HTML 页面中的布局元素看作是一个矩形的盒子，也就是一个盛装内容的容器。

CSS 盒子模型本质上是一个盒子，封装周围的 HTML 元素，它包括：边框、外边距、内边距、和 实际内容

### border

表示盒子的边框。

border可以设置元素的边框。边框有三部分组成:边框宽度(粗细) 边框样式 边框颜色

```
border : border-width border-style border-color
```

![image-20250402215543407](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402215543407.png)

边框样式 border-style 可以设置如下值：

- **none：没有边框即忽略所有边框的宽度（默认值）**
- solid：边框为单实线(最为常用的)
- dashed：边框为虚线
- dotted：边框为点线

边框分开写法：

```
border-top: 1px solid red;  /* 只设定上边框， 其余同理 */
```

**注意：**

边框会额外增加盒子的实际大小。因此我们有两种方案解决:
1. 测量盒子大小的时候,不量边框.
2. 如果测量的时候包含了边框,则需要 width/height 减去边框宽度

### padding

#### 简介

**表示盒子的内边距，即内容文本与边框之间的距离**

padding 属性用于设置内边距，即边框与内容之间的距离。

![image-20250402221709210](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402221709210.png)

#### 复合写法

padding 属性（简写属性）可以有一到四个值。

![image-20250402222348456](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250402222348456.png)

#### 盒子撑大问题

指定width或height需谨慎，可能会出现撑开问题。

**在指定了内容宽度width的情况下**，因为 **盒子宽度=width+左右内边距+左右边框宽度**，**这样网页元素的宽就会因为设置了内边距而增大，可能会超出父级盒子。**

**但是如果没有指定内容高度width**，那么盒子的宽度就继承父级盒子，因为盒子宽度已经定下来了不能更改，此时添加padding。那么盒子的宽度不会变化，内容高度width反而会变小。

**内容高度height同理**

**解决方案：**

css设置weight或height时就不要同时去设置padding了

### margin

#### 简介

**表示盒子的外边距，即盒子与盒子之间(相邻盒子之间)的距离**

同样也分为四个方向，也可以简写（按顺时针方向，默认上下一样，左右一样）。margin 简写方式代表的意义跟 padding 完全一致。

#### 居中对齐方式

外边距可以让块级盒子**水平居中**，但是必须满足两个条件：
① 盒子必须指定了宽度（width）。
② 盒子左右的外边距都设置为 auto 。

```css
.header{ width:960px; margin:0 auto;}
```

**垂直方向默认不生效的原因：普通流中垂直方向的高度计算方式不同，需要特定布局上下文。**

#### 外边距融合问题

使用 margin 定义块元素的**垂直外边距**时，可能会出现外边距的合并。
主要有两种情况:

1. 相邻块元素垂直外边距的合并
2. 嵌套块元素垂直外边距的塌陷

##### 相邻块元素垂直外边距的合并

当上下相邻的两个块元素（兄弟关系）相遇时，如果上面的元素有下外边距 margin-bottom，下面的元素有上外边距 margin-top ，则他们之间的垂直间距不是 margin-bottom 与 margin-top 之和。**取两个值中的较大者这种现象被称为相邻块元素垂直外边距的合并。**

![image-20250403133812558](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403133812558.png)

**解决方案：**

**尽量只给一个盒子添加 margin 值**

##### 嵌套块元素垂直外边距的塌陷

对于两个嵌套关系（父子关系）的块元素，父元素有上外边距同时子元素也有上外边距，此时子元素外边距直接消失，超出了父元素之中。

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403133318264.png" alt="image-20250403133318264" style="zoom: 67%;" />

**解决方案**：

- 给父元素设置个边框就可以了，设置边框子元素就不会超出了
- 给父元素添加padding也可以，原理同边框
- 给父元素设置 overflow:hidden 也可以

#### 清除内外边距

网页元素很多都带有默认的内外边距，而且不同浏览器默认的也不一致。因此我们在布局前，首先要清除下网页元素的内外边距。通过通配符选择器清除。

```css
css文件的第一段代码
* {
  padding:0;
  /* 清除内边距 */
  margin:0;
  /* 清除外边距 */
}
```

#### **注意**

**行内元素为了照顾兼容性，尽量只设置左右内外边距，不要设置上下内外边距。但是转换为块级和行内块元素就可以了，行内元素是没有高度的**

### 其他

#### 盒子大小计算

**HTML中的`width`和`height`属性控制的是盒子模型中的内容(content)部分**‌。在标准盒模型中，元素的宽度和高度属性仅定义了内容区域的大小，不包括内边距(padding)、边框(border)和外边距(margin)‌。

也就是说每个元素盒子模型

宽为：内容宽度width + 左右边框宽度 + 左右内边距

长为：内容高度height + 上下边框宽度 + 上下内边距   



## CSS浮动

### 简介

网页布局的本质——用 CSS 来摆放盒子。 把盒子摆放到相应位置。CSS 提供了三种传统布局方式(简单说,就是盒子如何进行排列顺序)：

- 普通流（标准流）
- 浮动
- 定位

**为什么需要浮动布局**

有很多的布局效果，标准流没有办法完成，此时就可以利用浮动完成布局。 因为浮动可以改变元素标签默认的排列方式.

**浮动最典型的应用：可以让多个块级元素一行内排列显示。**

**网页布局第一准则：多个块级元素纵向排列找标准流，多个块级元素横向排列找浮动**。

- 如何让多个块级盒子(div)水平排列成一行

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403162629326.png" alt="image-20250403162629326" style="zoom: 50%;" />

- 实现盒子之间对齐

![image-20250403162712313](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403162712313.png)

### 标准流

所谓的标准流: 就是标签按照规定好默认方式排列.

-  块级元素会独占一行，从上向下顺序排列。常用元素：div、hr、p、h1~h6、ul、ol、dl、form、table
- 行内元素会按照顺序，从左到右顺序排列，碰到父元素边缘则自动换行。 常用元素：span、a、i、em 等

### 浮动

#### 简介

float 属性用于创建浮动框，将其移动到一边，直到左边缘或右边缘**触及包含块或另一个浮动框的边缘。**

```css
选择器 { float: 属性值; }
```

![image-20250403163127616](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403163127616.png)

#### 浮动特性（重难点）

加了浮动之后的元素,会具有很多特性,需要我们掌握的.

1. **浮动元素会脱离标准流(脱标)。**比如一个元素如果添加了浮动，那么它就**脱离了标准流**，其它使用标准流的元素仍使用标准流但会忽略它而占据它原本的位置。
2. **浮动的元素会一行内显示并且元素顶部对齐**。也就是说块元素不再具有块元素的独占一行的特性，都是一行显示，而且它们顶部位置都是一致的，所以对齐了。
3. **浮动的元素会具有行内块元素的特性。**也就是说所有浮动元素就相当于行内块了，一行显示但是可以调节宽高等。

块级元素添加浮动后会变成一行显示，块级元素如果没有设置宽度，默认宽度和父级一样宽，但是添加浮动后，变成行内块了它的宽度根据内容来决定

行内元素添加浮动后，宽高可调节了。

**注意：**

- 浮动的盒子中间是没有缝隙的，是紧挨着一起的

#### 网页布局策略

为了约束浮动元素位置, 我们网页布局一般采取的策略是:

**先用标准流的父元素排列上下位置, 之后内部子元素采取浮动排列左右位置. 符合网页布局第一准侧.**

#### 浮动注意点

1. **浮动和标准流的父盒子搭配**。先用标准流的父元素排列上下位置, 之后内部子元素采取浮动排列左右位置
2. **一个元素浮动了，理论上其余的兄弟元素也要浮动。**一个盒子里面有多个子盒子，如果其中一个盒子浮动了，那么其他兄弟也应该浮动，以防止引起问题。浮动的盒子只会影响浮动盒子后面的标准流,不会影响前面的标准流.

### 清除浮动

#### 为什么需要清除浮动

因为有一种特殊情况我们不能确定父元素的高度，比如商品界面的商品可以一直往下滑，因为元素的高度默认为内容高度，因此**我们可以不指定父级元素的高度，让其根据内容自动确定。**

但是如果父级元素的子元素加了浮动属性，浮动了。**父级元素就不能根据子元素确定搞定了，就没有高度了。**

因此需要清除浮动，清除浮动之后，**父级就会根据浮动的子盒子自动检测高度。父级有了高度，就不会影响下面的标准流了**

#### 清除方法

##### 额外标签法

额外标签法也称为隔墙法，是 W3C 推荐的做法。
额外标签法会在浮动元素末尾添加一个空的标签。例如 <div style="clear:both"></div>

- 优点： 通俗易懂，书写方便
- 缺点： 添加许多无意义的标签，结构化较差

注意： 要求这个新的空标签必须是块级元素。

```
选择器{clear:属性值;}
```

![image-20250403183559241](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403183559241.png)

我们实际工作中， 几乎只用 clear: both;  清除浮动的策略是: 闭合浮动.

##### 父级添加 overflow

可以给父级添加 overflow 属性，将其属性值设置为 hidden、 auto 或 scroll 。
子不教,父之过,注意是给父元素添加代码

- 优点：代码简洁
- 缺点：无法显示溢出的部分

##### :after 伪元素法

:after 方式是额外标签法的升级版。也是给父元素添加

```css
.clearfix:after {
  content: "";
  display: block;
  height: 0;
  clear: both;
  visibility: hidden;
}
.clearfix { /* IE6、7 专有 */
  *zoom: 1;
}
```

- 优点：没有增加标签，结构更简单
- 缺点：照顾低版本浏览器

代表网站： 百度、淘宝网、网易等

##### 双伪元素清除浮动

```css
.clearfix:before,.clearfix:after {
  content: "";
  display: table;
}
.clearfix:after {
  clear: both;
}
.clearfix {
  *zoom: 1;
}
```

- 优点：代码更简洁
-  缺点：照顾低版本浏览器

代表网站：小米、腾讯等

#### 总结

为什么需要清除浮动？

① 父级没高度。
② 子盒子浮动了。
③ 影响下面布局了，我们就应该清除浮动了

![image-20250403184632513](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403184632513.png)

## 定位

### 简介

有些时候我们一个盒子中的元素位置通过内外边距很难处理，比如需要在一个盒子中的特定位置，并且压住其他盒子.

因此可以通过**position**属性实现对元素的定位，有四种定位方式。

**定位 = 定位模式 + 边偏移 。**

定位模式：

| 取值     | 含义     | 说明                                   |
| -------- | -------- | -------------------------------------- |
| static   | 默认值   | 按照常规文档流进行显示                 |
| relative | 相对定位 | 相对于标签原来的位置进行的定位         |
| absolute | 绝对定位 | 相对于第一个非static定位的父标签的定位 |
| fixed    | 固定定位 | 相对于浏览器窗品进行定位               |

边偏移：

![image-20250404135741652](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404135741652.png)

### 静态定位

静态定位是元素的默认定位方式，就是没有定位。

- 静态定位按照标准流特性摆放位置，它没有边偏移

- 静态定位在布局时很少用到

### 相对定位

- 它是**相对于自己原来的位置来移动**的（移动位置的时候参照点是自己原来的位置）。
- **不脱标**。原来在标准流的位置继续占有，后面的盒子仍然以标准流的方式对待它。

### 绝对定位

#### 简介

绝对定位是元素在移动位置的时候，是相对于它父元素来说的。

- 绝对定位实际定位时是相对**第一个具有定位的父元素的，浏览器是顶级父元素**
- **绝对定位会脱标**

一般情况下，使用**子绝父相**：

也就是说如果我们要使用绝对定位的话，就要将父元素设置为相对定位。

原因是父元素肯定不能用绝对定位的。

#### 绝对定位盒子居中

加了绝对定位的盒子不能通过 margin:0 auto 水平居中，但是可以通过以下计算方法实现水平和垂直居中。

- left: 50%;：让盒子的左侧移动到父级元素的水平中心位置。
- margin-left: -100px;：让盒子向左移动自身宽度的一半。

垂直居中同理

### 固定定位

#### 简介

固定定位是元素固定于浏览器可视区的位置。**主要使用场景： 可以在浏览器页面滚动时元素的位置不会改变。**

- **以浏览器的可视窗口为参照点移动元素**。跟父元素没有任何关系，不随滚动条滚动。
- **脱标**，固定定位不在占有原先的位置。

#### 固定定位小技巧： 固定在版心右侧位置。

让固定定位的盒子 left: 50%. 走到浏览器可视区（也可以看做版心） 的一半位置。

让固定定位的盒子 margin-left: 版心宽度的一半距离。 多走 版心宽度的一半位置
就可以让固定定位的盒子贴着版心右侧对齐了。

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404141004049.png" alt="image-20250404141004049" style="zoom:67%;" />

#### z-index

在使用定位布局时，可能会出现盒子重叠的情况。此时，可以使用 z-index 来控制盒子的前后次序 (z轴)

```css
选择器 { z-index: 1; }
```

- 数值可以是正整数、负整数或 0, 默认是 auto，数值越大，盒子越靠上
- 如果属性值相同，则按照书写顺序，后来居上
- 数字后面不能加单位
- 只有定位的盒子才有 z-index 属性

### 定位扩展

#### 定位特殊特性

绝对定位和固定定位也和浮动类似。

1. 行内元素添加绝对或者固定定位，可以直接设置高度和宽度。
2. 块级元素添加绝对或者固定定位，如果不给宽度或者高度，默认大小是内容的大小。

#### 脱标的盒子不会触发外边距塌陷

浮动元素、绝对定位(固定定位）元素的都不会触发外边距合并的问题。

#### 绝对定位（固定定位）会完全压住盒子

**浮动元素不同，只会压住它下面标准流的盒子，但是不会压住下面标准流盒子里面的文字（图片）**

但是绝对定位（固定定位） 会压住下面标准流所有的内容。

浮动之所以不会压住文字，因为浮动产生的目的最初是为了做文字环绕效果的。 文字会围绕浮动元素。

## CSS扩展属性

### 元素的显示和隐藏

#### display

通过display属性设置元素是否显示，以及是否独占一行。常用取值：

| 取值         | 含义                               | 说明                                 |
| ------------ | ---------------------------------- | ------------------------------------ |
| none         | 不显示                             |                                      |
| inline       | 显示为内联元素，行级元素的默认值   | 将块级元素变为行级元素，不再独占一行 |
| block        | 显示为块级元素，块级元素的默认值   | 将行级元素变为块级元素，独占一行     |
| inline-block | 显示为内联元素，但是可以设置宽和高 | 在inline基础上允许设置宽度和高度     |

注意：

行级元素是无法设置宽度和高度的，可以为行级元素设置 `display:inline-block` ,然后就可以设置宽和高了

#### visibility

也可以通过visibility属性设置元素的显示和隐藏。常用属性：

| 取值    | 含义 | 说明 |
| ------- | ---- | ---- |
| visible | 显示 |      |
| hidden  | 隐藏 |      |

**注意：**

- display隐藏时不再占据页面中的空间，后面的元素会占用其位置
- visibility隐藏时会占据页面中的空间，位置还保留在页面中，只是不显示

#### overflow 溢出

overflow 属性指定了如果内容溢出一个元素的框（超过其指定高度及宽度） 时，会发生什么。

![image-20250404144317521](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404144317521.png)

一般情况下，我们都不想让溢出的内容显示出来，因为溢出的部分会影响布局。但是如果有定位的盒子， 请慎用overflow:hidden 因为它会隐藏多余的部分。

### 轮廓属性

## CSS高级技巧

### 精灵图

### 字体图标

### CSS三角

### 用户界面样式

所谓的界面样式，就是更改一些用户操作样式，以便提高更好的用户体验。

- 更改用户的鼠标样式
- 表单轮廓
- 防止表单域拖拽

### vertical-align 属性应用

CSS 的 vertical-align 属性使用场景： 经常用于设置图片或者表单(行内块元素）和文字垂直对齐。

官方解释： 用于设置一个元素的垂直对齐方式，但是它只针对于行内元素或者行内块元素有效。

![image-20250404150716531](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404150716531.png)

![image-20250404150809392](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404150809392.png)

### 溢出的文字省略号显示

1. 单行文本溢出显示省略号

![image-20250404151004935](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404151004935.png)

```css
/*1. 先强制一行内显示文本*/
white-space: nowrap; （ 默认 normal 自动换行）
/*2. 超出的部分隐藏*/
overflow: hidden;
/*3. 文字用省略号替代超出的部分*/
text-overflow: ellipsis;
```

2. 多行文本溢出显示省略号

![image-20250404151025406](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404151025406.png)

```css
overflow: hidden;
text-overflow: ellipsis;
/* 弹性伸缩盒子模型显示 */
display: -webkit-box;
/* 限制在一个块元素显示的文本的行数 */
-webkit-line-clamp: 2;
/* 设置或检索伸缩盒对象的子元素的排列方式 */
-webkit-box-orient: vertical;
```

更推荐让后台人员来做这个效果，因为后台人员可以设置显示多少个字，操作更简单。

### 常见布局技巧

#### margin负值运用

两盒子相邻边框变粗了，直接 margin-left:-1 让边框重叠。

#### 文字环绕图片

因为

![image-20250404151717168](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404151717168.png)

#### 行内块巧妙运用

我们想让一些元素都居中对齐，可以将这些元素指定为行内块，然后父级元素指定text-align就可以影响的这些具有行特性的元素了。

![image-20250404152848527](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404152848527.png)



### flex布局



# HTML5和CSS3新特性

## HTML5新特性

### 语义化标签

 <header>：头部标签
 <nav>：导航标签
 <article>：内容标签
 <section>：定义文档某个区域
 <aside>：侧边栏标签
 <footer>：尾部标签

### 多媒体标签

### 表单属性

## CSS3新特性

### 新增选择器

#### 属性选择器

属性选择器可以根据元素特定属性的来选择元素。 这样就可以不用借助于类或者id选择器。

![image-20250404153423163](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404153423163.png)

注意：类选择器、属性选择器、伪类选择器，权重为 10。

#### 结构伪类选择器

结构伪类选择器主要根据文档结构来选择器元素， 常用于根据父级选择器里面的子元素

![image-20250404183333072](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404183333072.png)

区别：
1. nth-child 对父元素里面所有孩子排序选择（序号是固定的） 先找到第n个孩子，然后看看是否和E匹配
2. nth-of-type 对父元素里面指定子元素进行排序选择。 先去匹配E ，然后再根据E 找第n个孩子

#### 伪元素选择器（重点）

伪元素选择器可以帮助我们利用CSS创建新标签元素，而不需要HTML标签，从而简化HTML结构。

![image-20250404153539121](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404153539121.png)

**注意：**

- before 和 after 创建一个元素，但是属于行内元素
- 新创建的这个元素在文档树中是找不到的，所以我们称为伪元素
- 语法： element::before {}
- before 和 after **必须有 content 属性**
- before 在父元素**内容的前面**创建元素，after 在父元素**内容的后面**插入元素
- 伪元素选择器和标签选择器一样，权重为 1

![image-20250404185109899](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404185109899.png)

### 盒子模型

CSS3 中可以通过 box-sizing 来指定盒模型，有2个值：即可指定为 content-box、border-box，这样我们计算盒子大小的方式就发生了改变。

可以分成两种情况：

1. box-sizing: content-box 盒子大小为 width + padding + border （以前默认的）
2. box-sizing: border-box 盒子大小为 width

如果盒子模型我们改为了box-sizing: border-box ， 那padding和border就不会撑大盒子了（前提padding和border不会超过width宽度）

# 其他

## emment语法

### 快速生成HTML结构语法

- **生成标签直接输入标签名** 按tab键即可 比如 div 然后tab 键， 就可以生成 <div></div>
- 如果想要**生成多个相同标签** 加上 * 就可以了 比如 div*3 就可以快速生成3个div
- 如果有**父子级关系的标签**，可以用 > 比如 ul > li就可以了
- 如果有**兄弟关系的标签**，用 + 就可以了 比如 div+p
- 如果生成带有**类名或者id名字**的， 直接写 .demo 或者 #two   tab 键就可以了
- 如果生成的div 类名是**有顺序的**， 可以用 自增符号 $
- 如果想要**在生成的标签内部写内容**可以用 { } 表示

### 快速生成CSS样式语法

CSS 基本采取简写形式即可.
- 比如 w200 按tab 可以 生成 width: 200px;
- 比如 lh26px 按tab 可以生成 line-height: 26px;

### 快速格式化代码

shift+alt+f

使用插件保存时自动格式化

## ps基本操作

![image-20250403141412062](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250403141412062.png)

因为网页美工大部分效果图都是利用 PS（Photoshop）来做的，所以以后我们大部分切图工作都是在 PS 里面完成。

- 文件打开 ：可以打开我们要测量的图片
- Ctrl+R：可以打开标尺，或者 视图标尺
- 右击标尺，把里面的单位改为像素
- Ctrl+ 加号(+)可以放大视图， Ctrl+ 减号(-)可以缩小视图
- 按住空格键，鼠标可以变成小手，拖动 PS 视图
- 用选区拖动 可以测量大小
- Ctrl+ D 可以取消选区，或者在旁边空白处点击一下也可以取消选区

## ps切图



## 常用模块命名

![image-20250404180319408](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250404180319408.png)

# 学习杂项记录

浮动是将元素移动到左或右两边，直到左边缘或右边缘**触及包含块或另一个浮动框的边缘。**

盒子模型一个盒子包括了如下部分，外边距也是盒子的一部分

![盒子模型](D:\E\学习\Go\笔记\技术栈\前端\assets\盒子模型.png)



# 问题记录

## lin-height

hight不会继承，元素的高度默认就是内容的高度，当时行高line-height会继承，因此可能出现元素的高度不对的情况，就是继承了父级的行高。

## 浮动元素不会压文字

浮动元素不同，只会压住它下面标准流的盒子，但是不会压住下面标准流盒子里面的文字（图片）
