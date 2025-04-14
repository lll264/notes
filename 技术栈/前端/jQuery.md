# 简介

## 概述

jQuery 是一个高效、精简并且功能丰富的 JavaScript 工具库。它提供的 API 易于使用且兼容众多浏览器，这让诸如 HTML 文档遍历和操作、事件处理、动画和 Ajax 操作更加简单。目前超过90%的网站都使用了jQuery库，jQuery的宗旨：写的更少，做得更多！

## 基本使用

- 本地引入：将jQuery下载下来，然后导入项目中，使用script标签进行引用

- CDN引入：使用远程CDN资源库在线引入，避免了文件下载（本教程所采用）

```html
<head>
    <script src="jquery-1.9.1.min.js"></script>
</head>    
```

```html
<head>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
</head>    
```

**快速使用**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>jQuery</title>
</head>
<body>

    <div></div>    
    
<script>
    $(function () {
        // 请将jQuery代码书写在这里 ...
        alert('Hello,World!');
    });
    $('div').hide()
</script>
</body>
</html>
```

 `$()` 或 jQuery()，jQuery定义了这个全局的函数供我们调用，它既可作为一般函数调用，且传递的参数类型不同/格式不同，功能就完全不同，也可作为对象调用其定义好的方法，此时 `$` 就是一个工具对象。

**jQuery对象**

执行jQuery核心函数返回的对象，**jQuery对象是包含dom元素对象的伪数组(可能只有一个元素)**。比如$('div')执行返回的就是一个jQuery对象。不过dom对象和jQuery对象可以相互转换。

```js
// 1.DOM对象转换为 jQuery对象
$('video');
var myvideo = document.querySelector('video');
$(myvideo)
// 2.jQuery对象转换为DOM对象
$('video')[0]
$('video').get(0)
```

# 常用API

## jQuery常用属性和方法

**index**

获取当前对象的索引号

### 拷贝对象

```js
$.extend([deep], target, object1, [objectN])
```

- deep: 如果设为true 为深拷贝， 默认为false 浅拷贝
- target: 要拷贝的目标对象
- object1:待拷贝到第一个对象的对象。
- objectN:待拷贝到第N个对象的对象。

## 选择器

原生 JS 获取元素方式很多，很杂，而且兼容性情况不一致，因此 jQuery 给我们做了封装，使获取元素统一标准。

```js
$(“选择器”) // 里面选择器直接写 CSS 选择器即可，但是要加引号
```

### 基础选择器

![image-20250408144703502](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408144703502.png)

### 层级选择器

![image-20250408144753184](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408144753184.png)

### 筛选选择器

![image-20250408144900851](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408144900851.png)

### 筛选方法

![image-20250408145231913](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408145231913.png)

## 属性操作

```js
//prop 获取或设置元素自带属性
prop("属性")
prop("属性", "值")
//attr获取或修改自定义属性
attr("name")
attr("name", "val")
```

## 内容文本

主要针对元素的内容还有表单的值操作。

**普通元素内容 html()**（ 相当于原生inner HTML)

```js
html()// 获取元素的内容
html("内容")   // 设置元素的内容
```

**普通元素文本内容 text()**   (相当与原生 innerText)

```js
text()// 获取元素的文本内容
html("内容")   // 设置元素的内容
```

 **表单的值 val()**（ 相当于原生value)

```js
val() // 获取表单的值
val("内容")   // 设置表单的值
```



## 样式操作

### 隐式迭代

在操作jquery对象时，默认会遍历这个伪数组，从而全部执行这个操作。

### css()

jQuery 可以使用 css 方法来修改简单元素样式； 也可以操作类，修改多个样式。

```js
//参数只写属性名，则是返回属性值
$(this).css("color");

//参数是属性名，属性值，逗号分隔，是设置一组样式，属性必须加引号，值如果是数字可以不用跟单位和引号
$(this).css("color", "red");

//参数是属性名，属性值，逗号分隔，是设置一组样式，属性必须加引号，值如果是数字可以不用跟单位和引号
$(this).css({ "color":"white","font-size":"20px"});

//设置类样式
//添加类
$(“div”).addClass("current");
//移除类
$(“div”).removeClass("current");
//切换类
$(“div”).toggleClass("current");
```

## 元素操作

### 遍历

两种格式

```js
$("div").each(function (index, domEle) { xxx; })
$.each(object，function (index, element) { xxx; })         

$("div").each(function(i, domEle) {
    $(domEle).css("color", red);
})
$.each("div", function (index, domEle) {
    console.log(index, domEle);
});
```

### 创建和添加

```js
//1.创建元素
var li = $("<li>我是后来创建的li</li>");
//2.添加元素
//内部添加
$("ul").append(li);  //内部添加并且放到内容的最后面 
$("ul").prepend(li); // 内部添加并且放到内容的最前面
//部添加
var div = $("<div>我是后妈生的</div>");
$(".test").after(div);
$(".test").before(div);
```

### 删除

```js
$("ul").remove(); //删除匹配的元素本身
$("ul").empty();  //删除匹配的元素集合中所有的子节点
$("ul").html(""); //清空匹配的元素内容
```

## 尺寸位置

### 尺寸

![image-20250408154859166](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250408154859166.png)

 以上参数为空，则是获取相应值，返回的是数字型。
 如果参数为数字，则是修改相应值。
 参数可以不必写单位。

### 位置

位置主要有三个： offset()、position()、scrollTop()/scrollLeft()

#### offset() 设置或获取元素偏移

- offset() 方法设置或返回被选元素相对于文档的偏移坐标，跟父级没有关系。
- 该方法有2个属性 left、top 。offset().top 用于获取距离文档顶部的距离，offset().left 用于获取距离文档左侧的距离。
- **可以设置元素的偏移：offset({ top: 10, left: 30 });**

#### position() 获取元素偏移

- position() 方法用于返回被选元素相对于带有定位的父级偏移坐标，如果父级都没有定位，则以文档为准。
- 该方法有2个属性 left、top。position().top 用于获取距离定位父级顶部的距离，position().left 用于获取距离定位父级左侧的距离。
- 该方法只能获取不能设置。

#### scrollTop()/scrollLeft() 

设置或获取元素被卷去的头部和左侧

- scrollTop() 方法设置或返回被选元素被卷去的头部。
- 不跟参数是获取，参数为不带单位的数字则是设置被卷去的头部。

## 动画效果

# 事件

## 事件绑定

单个事件注册

```js
element.事件(function() {})
```

on绑定多个或单个事件

```js
element.on(events,[selector],fn)
```

1. events:一个或多个用空格分隔的事件类型，如"click"或"keydown" 。
2. selector: 元素的子元素选择器 。
3. fn:回调函数 即绑定在元素身上的侦听函数

```js
//多个事件处理程序不同
$("div").on({
    mouseenter: function() {
        $(this).css("background", "skyblue");
    },
    click: function() {
        $(this).css("background", "purple");
    },
    mouseleave: function() {
        $(this).css("background", "blue");
    }
});
//如果事件处理程序相同
$("div").on("mouseenter mouseleave", function() {
    $(this).toggleClass("current");
});
```

## on事件委托

事件委派的定义就是，把原来加给子元素身上的事件绑定在父元素身上，就是把事件委派给父元素。

```js
// $("ul li").click();
$("ul").on("click", "li", function() {
    alert(11);
});
```

## 动态元素绑定事件

on可以给未来动态创建添加的元素绑定事件

```js
// $("ol li").click(function() {
//     alert(11);
// })
$("ol").on("click", "li", function() {
    alert(11);
})
var li = $("<li>我是后来创建的</li>");
//添加了li元素后，li自动绑定了上面的事件
$("ol").append(li);
```

## 事件解绑

off() 方法可以移除通过 on() 方法添加的事件处理程序。

```js
$("p").off() // 解绑p元素所有事件处理程序
$("p").off( "click") // 解绑p元素上面的点击事件 后面的 foo 是侦听函数名
$("ul").off("click", "li"); // 解绑事件委托
```

如果有的事件只想触发一次， 可以使用 one() 来绑定事件。

## 自动触发事件

有些事件希望自动触发, 比如轮播图自动播放功能跟点击右侧按钮一致。可以利用定时器自动触发右侧按钮点击事件，不必鼠标点击触发。

```js
element.click()  // 第一种简写形式
element.trigger("type") // 第二种自动触发模式

$("p").on("click", function () {
  alert("hi~");
});
$("p").trigger("click"); // 此时自动触发点击事件，不需要鼠标点击
```

## 事件对象

事件被触发，就会有事件对象的产生。

- 阻止默认行为：event.preventDefault() 或者 return  false
- 阻止冒泡： event.stopPropagation()

```js
$("div").on("click", function(event) {
    // console.log(event);
    console.log("点击了div");
    event.stopPropagation();
})
```



# 实例

## jQuery排他思想

```js
$(this).css(“color”,”red”);
$(this).siblings(). css(“color”,””);
```

