# 模板与渲染

`html/template`包实现了数据驱动的模板，用于生成可防止代码注入的安全的HTML内容。它提供了和`text/template`包相同的接口，Go语言中输出HTML的场景都应使用`html/template`这个包。

在一些前后端不分离的Web架构中，我们通常需要在后端将一些数据渲染到HTML文档中，从而实现动态的网页（网页的布局和样式大致一样，但展示的内容并不一样）效果。

我们这里说的模板可以理解为事先定义好的HTML文档文件，模板渲染的作用机制可以简单理解为文本替换操作–使用相应的数据去替换HTML文档中事先准备好的标记。

很多编程语言的Web框架中都使用各种模板引擎，比如Python语言中Flask框架中使用的jinja2模板引擎。

## Go语言的模板引擎介绍

Go语言内置了文本模板引擎`text/template`和用于HTML文档的`html/template`。它们的作用机制可以简单归纳如下：

1. 模板文件通常定义为`.tmpl`和`.tpl`为后缀（也可以使用其他的后缀），必须使用`UTF8`编码。
2. 模板文件中使用`{{`和`}}`包裹和标识需要传入的数据。
3. 传给模板这样的数据就可以通过点号（`.`）来访问，如果数据是复杂类型的数据，可以通过{ { .FieldName }}来访问它的字段。
4. 除`{{`和`}}`包裹的内容外，其他内容均不做修改原样输出。

## 模板引擎的使用

Go语言模板引擎的使用可以分为三部分：定义模板文件、解析模板文件和模板渲染.

### 1.定义模板文件

其中，定义模板文件时需要我们按照相关语法规则去编写，后文会详细介绍。

### 2.解析模板文件

上面定义好了模板文件之后，可以使用下面的常用方法去解析模板文件，得到模板对象：

```go
func (t *Template) Parse(src string) (*Template, error)
func ParseFiles(filenames ...string) (*Template, error)
func ParseGlob(pattern string) (*Template, error)
```

当然，你也可以使用`func New(name string) *Template`函数创建一个名为`name`的模板，然后对其调用上面的方法去解析模板字符串或模板文件。

### 3.模板渲染

渲染模板简单来说就是使用数据去填充模板，当然实际上可能会复杂很多。

```go
func (t *Template) Execute(wr io.Writer, data interface{}) error
func (t *Template) ExecuteTemplate(wr io.Writer, name string, data interface{}) error
```

### 基本示例

#### 定义模板文件

我们按照Go模板语法定义一个`hello.tmpl`的模板文件，内容如下：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello</title>
</head>
<body>
    <p>Hello {{.}}</p>
</body>
</html>
```

#### 解析和渲染模板文件

然后我们创建一个`main.go`文件，在其中写下HTTP server端代码如下：

```go
// main.go

func sayHello(w http.ResponseWriter, r *http.Request) {
	// 解析指定文件生成模板对象
	tmpl, err := template.ParseFiles("./hello.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	// 利用给定数据渲染模板，并将结果写入w
	tmpl.Execute(w, "沙河小王子")
}
func main() {
	http.HandleFunc("/", sayHello)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		fmt.Println("HTTP server failed,err:", err)
		return
	}
}
```

将上面的`main.go`文件编译执行，然后使用浏览器访问`http://127.0.0.1:9090`就能看到页面上显示了“Hello 沙河小王子”。 这就是一个最简单的模板渲染的示例，Go语言模板引擎详细用法请往下阅读。

## 模板语法

### {{.}}

模板语法都包含在`{{`和`}}`中间，其中`{{.}}`中的点表示当前对象。

当我们传入一个结构体对象时，我们可以根据`.`来访问结构体的对应字段。例如：

```go
// main.go

type UserInfo struct {
	Name   string
	Gender string
	Age    int
}

func sayHello(w http.ResponseWriter, r *http.Request) {
	// 解析指定文件生成模板对象
	tmpl, err := template.ParseFiles("./hello.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	// 利用给定数据渲染模板，并将结果写入w
	user := UserInfo{
		Name:   "小王子",
		Gender: "男",
		Age:    18,
	}
	tmpl.Execute(w, user)
}
```

模板文件`hello.tmpl`内容如下：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello</title>
</head>
<body>
    <p>Hello {{.Name}}</p>
    <p>性别：{{.Gender}}</p>
    <p>年龄：{{.Age}}</p>
</body>
</html>
```

同理，当我们传入的变量是map时，也可以在模板文件中通过`.`根据key来取值。

### 注释

```template
{{/* a comment */}}
注释，执行时会忽略。可以多行。注释不能嵌套，并且必须紧贴分界符始止。
```

### pipeline

`pipeline`是指产生数据的操作。比如`{{.}}`、`{{.Name}}`等。Go的模板语法中支持使用管道符号`|`链接多个命令，用法和unix下的管道类似：`|`前面的命令会将运算结果(或返回值)传递给后一个命令的最后一个位置。

**注意：**并不是只有使用了`|`才是pipeline。Go的模板语法中，`pipeline的`概念是传递数据，只要能产生数据的，都是`pipeline`。

### 变量

我们还可以在模板中声明变量，用来保存传入模板的数据或其他语句生成的结果。具体语法如下：

```template
$obj := {{.}}
```

其中`$obj`是变量的名字，在后续的代码中就可以使用该变量了。

### 移除空格

有时候我们在使用模板语法的时候会不可避免的引入一下空格或者换行符，这样模板最终渲染出来的内容可能就和我们想的不一样，这个时候可以使用`{{-`语法去除模板内容左侧的所有空白符号， 使用`-}}`去除模板内容右侧的所有空白符号。

例如：

```template
{{- .Name -}}
```

**注意：**`-`要紧挨`{{`和`}}`，同时与模板值之间需要使用空格分隔。

### 条件判断

Go模板语法中的条件判断有以下几种:

```template
{{if pipeline}} T1 {{end}}

{{if pipeline}} T1 {{else}} T0 {{end}}

{{if pipeline}} T1 {{else if pipeline}} T0 {{end}}
```

### range

Go的模板语法中使用`range`关键字进行遍历，有以下两种写法，其中`pipeline`的值必须是数组、切片、字典或者通道。

```template
{{range pipeline}} T1 {{end}}
如果pipeline的值其长度为0，不会有任何输出

{{range pipeline}} T1 {{else}} T0 {{end}}
如果pipeline的值其长度为0，则会执行T0。

{{$i, $v := range .}} {{$i}} {{$v}} {{end}}
```

### with

```template
{{with pipeline}} T1 {{end}}
如果pipeline为empty不产生输出，否则将dot设为pipeline的值并执行T1。不修改外面的dot。

{{with pipeline}} T1 {{else}} T0 {{end}}
如果pipeline为empty，不改变dot并执行T0，否则dot设为pipeline的值并执行T1。
```

### 预定义函数

执行模板时，函数从两个函数字典中查找：首先是模板函数字典，然后是全局函数字典。一般不在模板内定义函数，而是使用Funcs方法添加函数到模板里。

预定义的全局函数如下：

```template
and
    函数返回它的第一个empty参数或者最后一个参数；
    就是说"and x y"等价于"if x then y else x"；所有参数都会执行；
or
    返回第一个非empty参数或者最后一个参数；
    亦即"or x y"等价于"if x then x else y"；所有参数都会执行；
not
    返回它的单个参数的布尔值的否定
len
    返回它的参数的整数类型长度
index
    执行结果为第一个参数以剩下的参数为索引/键指向的值；
    如"index x 1 2 3"返回x[1][2][3]的值；每个被索引的主体必须是数组、切片或者字典。
print
    即fmt.Sprint
printf
    即fmt.Sprintf
println
    即fmt.Sprintln
html
    返回与其参数的文本表示形式等效的转义HTML。
    这个函数在html/template中不可用。
urlquery
    以适合嵌入到网址查询中的形式返回其参数的文本表示的转义值。
    这个函数在html/template中不可用。
js
    返回与其参数的文本表示形式等效的转义JavaScript。
call
    执行结果是调用第一个参数的返回值，该参数必须是函数类型，其余参数作为调用该函数的参数；
    如"call .X.Y 1 2"等价于go语言里的dot.X.Y(1, 2)；
    其中Y是函数类型的字段或者字典的值，或者其他类似情况；
    call的第一个参数的执行结果必须是函数类型的值（和预定义函数如print明显不同）；
    该函数类型值必须有1到2个返回值，如果有2个则后一个必须是error接口类型；
    如果有2个返回值的方法返回的error非nil，模板执行会中断并返回给调用模板执行者该错误；
```

### 比较函数

布尔函数会将任何类型的零值视为假，其余视为真。

下面是定义为函数的二元比较运算的集合：

```template
eq      如果arg1 == arg2则返回真
ne      如果arg1 != arg2则返回真
lt      如果arg1 < arg2则返回真
le      如果arg1 <= arg2则返回真
gt      如果arg1 > arg2则返回真
ge      如果arg1 >= arg2则返回真
```

为了简化多参数相等检测，eq（只有eq）可以接受2个或更多个参数，它会将第一个参数和其余参数依次比较，返回下式的结果：

```template
{{eq arg1 arg2 arg3}}
```

比较函数只适用于基本类型（或重定义的基本类型，如"type Celsius float32"）。但是，整数和浮点数不能互相比较。

### 自定义函数

Go的模板支持自定义函数。

```go
func sayHello(w http.ResponseWriter, r *http.Request) {
	htmlByte, err := ioutil.ReadFile("./hello.tmpl")
	if err != nil {
		fmt.Println("read html failed, err:", err)
		return
	}
	// 自定义一个夸人的模板函数
	kua := func(arg string) (string, error) {
		return arg + "真帅", nil
	}
	// 采用链式操作在Parse之前调用Funcs添加自定义的kua函数
	tmpl, err := template.New("hello").Funcs(template.FuncMap{"kua": kua}).Parse(string(htmlByte))
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}

	user := UserInfo{
		Name:   "小王子",
		Gender: "男",
		Age:    18,
	}
	// 使用user渲染模板，并将结果写入w
	tmpl.Execute(w, user)
}
```

我们可以在模板文件`hello.tmpl`中按照如下方式使用我们自定义的`kua`函数了。

```template
{{kua .Name}}
```

### 嵌套template

我们可以在template中嵌套其他的template。这个template可以是单独的文件，也可以是通过`define`定义的template。

举个例子： `t.tmpl`文件内容如下：

```template
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>tmpl test</title>
</head>
<body>
    
    <h1>测试嵌套template语法</h1>
    <hr>
    {{template "ul.tmpl"}}
    <hr>
    {{template "ol.tmpl"}}
</body>
</html>

{{ define "ol.tmpl"}}
<ol>
    <li>吃饭</li>
    <li>睡觉</li>
    <li>打豆豆</li>
</ol>
{{end}}
```

`ul.tmpl`文件内容如下：

```template
<ul>
    <li>注释</li>
    <li>日志</li>
    <li>测试</li>
</ul>
```

我们注册一个`templDemo`路由处理函数.

```go
http.HandleFunc("/tmpl", tmplDemo)
```

`tmplDemo`函数的具体内容如下：

```go
func tmplDemo(w http.ResponseWriter, r *http.Request) {
	tmpl, err := template.ParseFiles("./t.tmpl", "./ul.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	user := UserInfo{
		Name:   "小王子",
		Gender: "男",
		Age:    18,
	}
	tmpl.Execute(w, user)
}
```

**注意**：在解析模板时，被嵌套的模板一定要在后面解析，例如上面的示例中`t.tmpl`模板中嵌套了`ul.tmpl`，所以`ul.tmpl`要在`t.tmpl`后进行解析。

### block

```template
{{block "name" pipeline}} T1 {{end}}
```

`block`是定义模板`{{define "name"}} T1 {{end}}`和执行`{{template "name" pipeline}}`缩写，典型的用法是定义一组根模板，然后通过在其中重新定义块模板进行自定义。

定义一个根模板`templates/base.tmpl`，内容如下：

```template
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>Go Templates</title>
</head>
<body>
<div class="container-fluid">
    {{block "content" . }}{{end}}
</div>
</body>
</html>
```

然后定义一个`templates/index.tmpl`，“继承”`base.tmpl`：

```tempalte
{{template "base.tmpl"}}

{{define "content"}}
    <div>Hello world!</div>
{{end}}
```

然后使用`template.ParseGlob`按照正则匹配规则解析模板文件，然后通过`ExecuteTemplate`渲染指定的模板：

```go
func index(w http.ResponseWriter, r *http.Request){
	tmpl, err := template.ParseGlob("templates/*.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	err = tmpl.ExecuteTemplate(w, "index.tmpl", nil)
	if err != nil {
		fmt.Println("render template failed, err:", err)
		return
	}
}
```

如果我们的模板名称冲突了，例如不同业务线下都定义了一个`index.tmpl`模板，我们可以通过下面两种方法来解决。

1. 在模板文件开头使用`{{define 模板名}}`语句显式的为模板命名。
2. 可以把模板文件存放在`templates`文件夹下面的不同目录中，然后使用`template.ParseGlob("templates/**/*.tmpl")`解析模板。

### 修改默认的标识符

Go标准库的模板引擎使用的花括号`{{`和`}}`作为标识，而许多前端框架（如`Vue`和 `AngularJS`）也使用`{{`和`}}`作为标识符，所以当我们同时使用Go语言模板引擎和以上前端框架时就会出现冲突，这个时候我们需要修改标识符，修改前端的或者修改Go语言的。这里演示如何修改Go语言模板引擎默认的标识符：

```go
template.New("test").Delims("{[", "]}").ParseFiles("./t.tmpl")
```

## text/template与html/tempalte的区别

`html/template`针对的是需要返回HTML内容的场景，在模板渲染过程中会对一些有风险的内容进行转义，以此来防范跨站脚本攻击。

例如，我定义下面的模板文件：

```template
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello</title>
</head>
<body>
    {{.}}
</body>
</html>
```

这个时候传入一段JS代码并使用`html/template`去渲染该文件，会在页面上显示出转义后的JS内容。`<script>alert('嘿嘿嘿')</script>` 这就是`html/template`为我们做的事。

但是在某些场景下，我们如果相信用户输入的内容，不想转义的话，可以自行编写一个safe函数，手动返回一个`template.HTML`类型的内容。示例如下：

```go
func xss(w http.ResponseWriter, r *http.Request){
	tmpl,err := template.New("xss.tmpl").Funcs(template.FuncMap{
		"safe": func(s string)template.HTML {
			return template.HTML(s)
		},
	}).ParseFiles("./xss.tmpl")
	if err != nil {
		fmt.Println("create template failed, err:", err)
		return
	}
	jsStr := `<script>alert('嘿嘿嘿')</script>`
	err = tmpl.Execute(w, jsStr)
	if err != nil {
		fmt.Println(err)
	}
}
```

这样我们只需要在模板文件不需要转义的内容后面使用我们定义好的safe函数就可以了。

```template
{{ . | safe }}
```



# Gin框架介绍及使用

`Gin`是一个用Go语言编写的web框架。它是一个类似于`martini`但拥有更好性能的API框架, 由于使用了`httprouter`，速度提高了近40倍。 如果你是性能和高效的追求者, 你会爱上`Gin`。

## Gin框架介绍

Go世界里最流行的Web框架，[Github](https://github.com/gin-gonic/gin)上有`32K+`star。 基于[httprouter](https://github.com/julienschmidt/httprouter)开发的Web框架。 [中文文档](https://gin-gonic.com/zh-cn/docs/)齐全，简单易用的轻量级框架。

## Gin框架安装与使用

### 安装

下载并安装`Gin`:

```bash
go get -u github.com/gin-gonic/gin
```

### 第一个Gin示例：

```golang
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	// 创建一个默认的路由引擎
	r := gin.Default()
	// GET：请求方式；/hello：请求的路径
	// 当客户端以GET方法请求/hello路径时，会执行后面的匿名函数
	r.GET("/hello", func(c *gin.Context) {
		// c.JSON：返回JSON格式的数据
		c.JSON(200, gin.H{
			"message": "Hello world!",
		})
	})
	// 启动HTTP服务，默认在0.0.0.0:8080启动服务
	r.Run()
}
```

将上面的代码保存并编译执行，然后使用浏览器打开`127.0.0.1:8080/hello`就能看到一串JSON字符串。

## RESTful API

REST与技术无关，代表的是一种软件架构风格，REST是Representational State Transfer的简称，中文翻译为“表征状态转移”或“表现层状态转化”。

推荐阅读[阮一峰 理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)

简单来说，REST的含义就是客户端与Web服务器之间进行交互的时候，使用HTTP协议中的4个请求方法代表不同的动作。

- `GET`用来获取资源
- `POST`用来新建资源
- `PUT`用来更新资源
- `DELETE`用来删除资源。

只要API程序遵循了REST风格，那就可以称其为RESTful API。目前在前后端分离的架构中，前后端基本都是通过RESTful API来进行交互。

例如，我们现在要编写一个管理书籍的系统，我们可以查询对一本书进行查询、创建、更新和删除等操作，我们在编写程序的时候就要设计客户端浏览器与我们Web服务端交互的方式和路径。按照经验我们通常会设计成如下模式：

| 请求方法 |     URL      |     含义     |
| :------: | :----------: | :----------: |
|   GET    |    /book     | 查询书籍信息 |
|   POST   | /create_book | 创建书籍记录 |
|   POST   | /update_book | 更新书籍信息 |
|   POST   | /delete_book | 删除书籍信息 |

同样的需求我们按照RESTful API设计如下：

| 请求方法 |  URL  |     含义     |
| :------: | :---: | :----------: |
|   GET    | /book | 查询书籍信息 |
|   POST   | /book | 创建书籍记录 |
|   PUT    | /book | 更新书籍信息 |
|  DELETE  | /book | 删除书籍信息 |

Gin框架支持开发RESTful API的开发。

```go
func main() {
	r := gin.Default()
	r.GET("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "GET",
		})
	})

	r.POST("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "POST",
		})
	})

	r.PUT("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "PUT",
		})
	})

	r.DELETE("/book", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "DELETE",
		})
	})
}
```

开发RESTful API的时候我们通常使用[Postman](https://www.getpostman.com/)来作为客户端的测试工具。

## Gin渲染

### HTML渲染

我们首先定义一个存放模板文件的`templates`文件夹，然后在其内部按照业务分别定义一个`posts`文件夹和一个`users`文件夹。 `posts/index.html`文件的内容如下：

```template
{{define "posts/index.html"}}
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>posts/index</title>
</head>
<body>
    {{.title}}
</body>
</html>
{{end}}
```

`users/index.html`文件的内容如下：

```template
{{define "users/index.html"}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>users/index</title>
</head>
<body>
    {{.title}}
</body>
</html>
{{end}}
```

Gin框架中使用`LoadHTMLGlob()`或者`LoadHTMLFiles()`方法进行HTML模板渲染。

```go
func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/**/*")
	//r.LoadHTMLFiles("templates/posts/index.html", "templates/users/index.html")
	r.GET("/posts/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "posts/index.html", gin.H{
			"title": "posts/index",
		})
	})

	r.GET("users/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "users/index.html", gin.H{
			"title": "users/index",
		})
	})

	r.Run(":8080")
}
```

### 自定义模板函数

定义一个不转义相应内容的`safe`模板函数如下：

```go
func main() {
	router := gin.Default()
	router.SetFuncMap(template.FuncMap{
		"safe": func(str string) template.HTML{
			return template.HTML(str)
		},
	})
	router.LoadHTMLFiles("./index.tmpl")

	router.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.tmpl", "<a href='https://liwenzhou.com'>李文周的博客</a>")
	})

	router.Run(":8080")
}
```

在`index.tmpl`中使用定义好的`safe`模板函数：

```template
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>修改模板引擎的标识符</title>
</head>
<body>
<div>{{ . | safe }}</div>
</body>
</html>
```

### 静态文件处理

当我们渲染的HTML文件中引用了静态文件(.css js文件 图片等)时，我们只需要按照以下方式在渲染页面前调用`gin.Static`方法即可。

```go
func main() {
	r := gin.Default()
	r.Static("/static", "./static")
	r.LoadHTMLGlob("templates/**/*")
   // ...
	r.Run(":8080")
}
```

### 使用模板继承

Gin框架默认都是使用单模板，如果需要使用`block template`功能，可以通过`"github.com/gin-contrib/multitemplate"`库实现，具体示例如下：

首先，假设我们项目目录下的templates文件夹下有以下模板文件，其中`home.tmpl`和`index.tmpl`继承了`base.tmpl`：

```bash
templates
├── includes
│   ├── home.tmpl
│   └── index.tmpl
├── layouts
│   └── base.tmpl
└── scripts.tmpl
```

然后我们定义一个`loadTemplates`函数如下：

```go
func loadTemplates(templatesDir string) multitemplate.Renderer {
	r := multitemplate.NewRenderer()
	layouts, err := filepath.Glob(templatesDir + "/layouts/*.tmpl")
	if err != nil {
		panic(err.Error())
	}
	includes, err := filepath.Glob(templatesDir + "/includes/*.tmpl")
	if err != nil {
		panic(err.Error())
	}
	// 为layouts/和includes/目录生成 templates map
	for _, include := range includes {
		layoutCopy := make([]string, len(layouts))
		copy(layoutCopy, layouts)
		files := append(layoutCopy, include)
		r.AddFromFiles(filepath.Base(include), files...)
	}
	return r
}
```

我们在`main`函数中

```go
func indexFunc(c *gin.Context){
	c.HTML(http.StatusOK, "index.tmpl", nil)
}

func homeFunc(c *gin.Context){
	c.HTML(http.StatusOK, "home.tmpl", nil)
}

func main(){
	r := gin.Default()
	r.HTMLRender = loadTemplates("./templates")
	r.GET("/index", indexFunc)
	r.GET("/home", homeFunc)
	r.Run()
}
```

### 补充文件路径处理

关于模板文件和静态文件的路径，我们需要根据公司/项目的要求进行设置。可以使用下面的函数获取当前执行程序的路径。

```go
func getCurrentPath() string {
	if ex, err := os.Executable(); err == nil {
		return filepath.Dir(ex)
	}
	return "./"
}
```

### JSON渲染

```go
func main() {
	r := gin.Default()

	// gin.H 是map[string]interface{}的缩写
	r.GET("/someJSON", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.JSON(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreJSON", func(c *gin.Context) {
		// 方法二：使用结构体
		var msg struct {
			Name    string `json:"user"`
			Message string
			Age     int
		}
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.JSON(http.StatusOK, msg)
	})
	r.Run(":8080")
}
```

### XML渲染

注意需要使用具名的结构体类型。

```go
func main() {
	r := gin.Default()
	// gin.H 是map[string]interface{}的缩写
	r.GET("/someXML", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.XML(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreXML", func(c *gin.Context) {
		// 方法二：使用结构体
		type MessageRecord struct {
			Name    string
			Message string
			Age     int
		}
		var msg MessageRecord
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.XML(http.StatusOK, msg)
	})
	r.Run(":8080")
}
```

### YMAL渲染

```go
r.GET("/someYAML", func(c *gin.Context) {
	c.YAML(http.StatusOK, gin.H{"message": "ok", "status": http.StatusOK})
})
```

### protobuf渲染

```go
r.GET("/someProtoBuf", func(c *gin.Context) {
	reps := []int64{int64(1), int64(2)}
	label := "test"
	// protobuf 的具体定义写在 testdata/protoexample 文件中。
	data := &protoexample.Test{
		Label: &label,
		Reps:  reps,
	}
	// 请注意，数据在响应中变为二进制数据
	// 将输出被 protoexample.Test protobuf 序列化了的数据
	c.ProtoBuf(http.StatusOK, data)
})
```

## 获取参数

### 获取querystring参数

`querystring`指的是URL中`?`后面携带的参数，例如：`/user/search?username=小王子&address=沙河`。 获取请求的querystring参数的方法如下：

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.GET("/user/search", func(c *gin.Context) {
		username := c.DefaultQuery("username", "小王子")
		//username := c.Query("username")
		address := c.Query("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})
	r.Run()
}
```

### 获取form参数

当前端请求的数据通过form表单提交时，例如向`/user/search`发送一个POST请求，获取请求数据的方式如下：

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.POST("/user/search", func(c *gin.Context) {
		// DefaultPostForm取不到值时会返回指定的默认值
		//username := c.DefaultPostForm("username", "小王子")
		username := c.PostForm("username")
		address := c.PostForm("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})
	r.Run(":8080")
}
```

### 获取JSON参数

当前端请求的数据通过JSON提交时，例如向`/json`发送一个JSON格式的POST请求，则获取请求参数的方式如下：

```go
r.POST("/json", func(c *gin.Context) {
	// 注意：下面为了举例子方便，暂时忽略了错误处理
	b, _ := c.GetRawData()  // 从c.Request.Body读取请求数据
	// 定义map或结构体
	var m map[string]interface{}
	// 反序列化
	_ = json.Unmarshal(b, &m)

	c.JSON(http.StatusOK, m)
})
```

更便利的获取请求参数的方式，参见下面的 **参数绑定** 小节。

### 获取path参数

请求的参数通过URL路径传递，例如：`/user/search/小王子/沙河`。 获取请求URL路径中的参数的方式如下。

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.GET("/user/search/:username/:address", func(c *gin.Context) {
		username := c.Param("username")
		address := c.Param("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})

	r.Run(":8080")
}
```

### 获取文件

```go
func Upload(c *gin.Context) {
	file, err := c.FormFile("myFile")
	if err != nil {
		fmt.Println("c.FormFile err:", err)
		return
	}
	fmt.Println(file.Filename)
}
```

### 参数绑定Bind/ShouldBind

为了能够更方便的获取请求相关参数，提高开发效率，我们可以基于请求的`Content-Type`识别请求数据类型并利用反射机制自动提取请求中`QueryString`、`form表单`、`JSON`、`XML`等参数到结构体中。 下面的示例代码演示了`.ShouldBind()`强大的功能，它能够基于请求自动提取`JSON`、`form表单`和`QueryString`类型的数据，并把值绑定到指定的结构体对象。

```go
// Binding from JSON
type Login struct {
	User     string `form:"user" json:"user" binding:"required"`
	Password string `form:"password" json:"password" binding:"required"`
}

func main() {
	router := gin.Default()

	// 绑定JSON的示例 ({"user": "q1mi", "password": "123456"})
	router.POST("/loginJSON", func(c *gin.Context) {
		var login Login

		if err := c.ShouldBind(&login); err == nil {
			fmt.Printf("login info:%#v\n", login)
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// 绑定form表单示例 (user=q1mi&password=123456)
	router.POST("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// 绑定QueryString示例 (/loginQuery?user=q1mi&password=123456)
	router.GET("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// Listen and serve on 0.0.0.0:8080
	router.Run(":8080")
}
```

**注意**

`Bind` 函数与`ShouldBind`类似，也是用于将请求体中的数据绑定到Go结构体中。然而，与`ShouldBind`不同的是，`Bind`在遇到绑定错误时不会修改HTTP响应状态码或写入错误消息。相反，它会返回一个错误，这使得调用者可以根据需要处理错误，比如记录日志、返回自定义的错误响应等。

`ShouldBind`会按照下面的顺序解析请求中的数据完成绑定：

1. 如果是 `GET` 请求，只使用 `Form` 绑定引擎（`query`）。
2. 如果是 `POST` 请求，首先检查 `content-type` 是否为 `JSON` 或 `XML`，然后再使用 `Form`（`form-data`）。

## 文件上传

### 单个文件上传

文件上传前端页面代码：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>上传文件示例</title>
</head>
<body>
<form action="/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="f1">
    <input type="submit" value="上传">
</form>
</body>
</html>
```

后端gin框架部分代码：

```go
func main() {
	router := gin.Default()
	// 处理multipart forms提交文件时默认的内存限制是32 MiB
	// 可以通过下面的方式修改
	// router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// 单个文件
		file, err := c.FormFile("f1")
		if err != nil {
			c.JSON(http.StatusInternalServerError, gin.H{
				"message": err.Error(),
			})
			return
		}

		log.Println(file.Filename)
		dst := fmt.Sprintf("C:/tmp/%s", file.Filename)
		// 上传文件到指定的目录
		c.SaveUploadedFile(file, dst)
		c.JSON(http.StatusOK, gin.H{
			"message": fmt.Sprintf("'%s' uploaded!", file.Filename),
		})
	})
	router.Run()
}
```

### 多个文件上传

```go
func main() {
	router := gin.Default()
	// 处理multipart forms提交文件时默认的内存限制是32 MiB
	// 可以通过下面的方式修改
	// router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// Multipart form
		form, _ := c.MultipartForm()
		files := form.File["file"]

		for index, file := range files {
			log.Println(file.Filename)
			dst := fmt.Sprintf("C:/tmp/%s_%d", file.Filename, index)
			// 上传文件到指定的目录
			c.SaveUploadedFile(file, dst)
		}
		c.JSON(http.StatusOK, gin.H{
			"message": fmt.Sprintf("%d files uploaded!", len(files)),
		})
	})
	router.Run()
}
```

## 重定向

### HTTP重定向

HTTP 重定向很容易。 内部、外部重定向均支持。

```go
r.GET("/test", func(c *gin.Context) {
	c.Redirect(http.StatusMovedPermanently, "http://www.sogo.com/")
    c.Redirect(http.StatusMovedPermanently, "/hello02")
})
```

### 路由重定向

路由重定向，使用`HandleContext`：

```go
r.GET("/test", func(c *gin.Context) {
    // 指定重定向的URL
    c.Request.URL.Path = "/test2"
    r.HandleContext(c)
})
r.GET("/test2", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{"hello": "world"})
})
```

## Gin路由

### 普通路由

```go
r.GET("/index", func(c *gin.Context) {...})
r.POST("/login", func(c *gin.Context) {...})
r.PUT("/login", func(c *gin.Context) {...})
r.DELETE("/login", func(c *gin.Context) {...})
```

此外，还有一个可以匹配所有请求方法的`Any`方法如下：

```go
r.Any("/test", func(c *gin.Context) {...})
```

为没有配置处理函数的路由添加处理程序，默认情况下它返回404代码，下面的代码为没有匹配到路由的请求都返回`views/404.html`页面。

```go
r.NoRoute(func(c *gin.Context) {
		c.HTML(http.StatusNotFound, "views/404.html", nil)
	})
```

### 路由组

我们可以将拥有共同URL前缀的路由划分为一个路由组。习惯性一对`{}`包裹同组的路由，这只是为了看着清晰，你用不用`{}`包裹功能上没什么区别。

```go
func main() {
	r := gin.Default()
	userGroup := r.Group("/user")
	{
		userGroup.GET("/index", func(c *gin.Context) {...})
		userGroup.GET("/login", func(c *gin.Context) {...})
		userGroup.POST("/login", func(c *gin.Context) {...})

	}
	shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
	}
	r.Run()
}
```

路由组也是支持嵌套的，例如：

```go
shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
		// 嵌套路由组
		xx := shopGroup.Group("xx")
		xx.GET("/oo", func(c *gin.Context) {...})
	}
```

通常我们将路由分组用在划分业务逻辑或划分API版本时。

### 路由原理

Gin框架中的路由使用的是[httprouter](https://github.com/julienschmidt/httprouter)这个库。

其基本原理就是构造一个路由地址的前缀树。

## Gin中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。

### 定义中间件

Gin中的中间件必须是一个`gin.HandlerFunc`类型。func(*gin.Context)类型

#### 记录接口耗时的中间件

例如我们像下面的代码一样定义一个统计请求耗时的中间件。

```go
// StatCost 是一个统计耗时请求耗时的中间件
//使用返回一个函数的操作，方便传入参数
func StatCost() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()
		c.Set("name", "小王子") // 可以通过c.Set在请求上下文中设置值，后续的处理函数能够取到该值
		// 调用该请求的剩余处理程序, 后续程序处理完后回来继续执行
		c.Next()
		// 不调用该请求的剩余处理程序
		// c.Abort()
		// 计算耗时
		cost := time.Since(start)
		log.Println(cost)
	}
}

func main() {
    r := gin.Default()
    r.Get("/index", StatCost(), f1)
	r.GET("/index", f1)
    r.Run()
}
```

#### 记录响应体的中间件

我们有时候可能会想要记录下某些情况下返回给客户端的响应数据，这个时候就可以编写一个中间件来搞定。

```go
type bodyLogWriter struct {
	gin.ResponseWriter               // 嵌入gin框架ResponseWriter
	body               *bytes.Buffer // 我们记录用的response
}

// Write 写入响应体数据
func (w bodyLogWriter) Write(b []byte) (int, error) {
	w.body.Write(b)                  // 我们记录一份
	return w.ResponseWriter.Write(b) // 真正写入响应
}

// ginBodyLogMiddleware 一个记录返回给客户端响应体的中间件
// https://stackoverflow.com/questions/38501325/how-to-log-response-body-in-gin
func ginBodyLogMiddleware(c *gin.Context) {
	blw := &bodyLogWriter{body: bytes.NewBuffer([]byte{}), ResponseWriter: c.Writer}
	c.Writer = blw // 使用我们自定义的类型替换默认的

	c.Next() // 执行业务逻辑

	fmt.Println("Response body: " + blw.body.String()) // 事后按需记录返回的响应
}
```

#### 跨域中间件cors

推荐使用社区的https://github.com/gin-contrib/cors 库，一行代码解决前后端分离架构下的跨域问题。

**注意：** 该中间件需要注册在业务处理函数前面。

这个库支持各种常用的配置项，具体使用方法如下。

```go
package main

import (
  "time"

  "github.com/gin-contrib/cors"
  "github.com/gin-gonic/gin"
)

func main() {
  router := gin.Default()
  // CORS for https://foo.com and https://github.com origins, allowing:
  // - PUT and PATCH methods
  // - Origin header
  // - Credentials share
  // - Preflight requests cached for 12 hours
  router.Use(cors.New(cors.Config{
    AllowOrigins:     []string{"https://foo.com"},  // 允许跨域发来请求的网站
    AllowMethods:     []string{"GET", "POST", "PUT", "DELETE",  "OPTIONS"},  // 允许的请求方法
    AllowHeaders:     []string{"Origin", "Authorization", "Content-Type"},
    ExposeHeaders:    []string{"Content-Length"},
    AllowCredentials: true,
    AllowOriginFunc: func(origin string) bool {  // 自定义过滤源站的方法
      return origin == "https://github.com"
    },
    MaxAge: 12 * time.Hour,
  }))
  router.Run()
}
```

当然你可以简单的像下面的示例代码那样使用默认配置，允许所有的跨域请求。

```go
func main() {
  router := gin.Default()
  // same as
  // config := cors.DefaultConfig()
  // config.AllowAllOrigins = true
  // router.Use(cors.New(config))
  router.Use(cors.Default())
  router.Run()
}
```

### 注册中间件

在gin框架中，我们可以为每个路由添加任意数量的中间件。

#### 为全局路由注册

```go
func main() {
	// 新建一个没有任何默认中间件的路由
	r := gin.New()
	// 注册一个全局中间件
	r.Use(StatCost())
	
	r.GET("/test", func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
	r.Run()
}
```

#### 为某个路由单独注册

```go
// 给/test2路由单独注册中间件（可注册多个）
	r.GET("/test2", StatCost(), func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
```

#### 为路由组注册中间件

为路由组注册中间件有以下两种写法。

写法1：

```go
shopGroup := r.Group("/shop", StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

写法2：

```go
shopGroup := r.Group("/shop")
shopGroup.Use(StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

### 中间件注意事项

#### gin默认中间件

`gin.Default()`默认使用了`Logger`和`Recovery`中间件，其中：

- `Logger`中间件将日志写入`gin.DefaultWriter`，即使配置了`GIN_MODE=release`。
- `Recovery`中间件会recover任何`panic`。如果有panic的话，会写入500响应码。

如果不想使用上面两个默认的中间件，可以使用`gin.New()`新建一个没有任何默认中间件的路由。

#### gin中间件中使用goroutine

当在中间件或`handler`中启动新的`goroutine`时，**不能使用**原始的上下文（c *gin.Context），必须使用其只读副本（`c.Copy()`）。防止c被修改

## 运行多个服务

我们可以在多个端口启动服务，例如：

```go
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/gin-gonic/gin"
	"golang.org/x/sync/errgroup"
)

var (
	g errgroup.Group
)

func router01() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 01",
			},
		)
	})

	return e
}

func router02() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 02",
			},
		)
	})

	return e
}

func main() {
	server01 := &http.Server{
		Addr:         ":8080",
		Handler:      router01(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	server02 := &http.Server{
		Addr:         ":8081",
		Handler:      router02(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}
   // 借助errgroup.Group或者自行开启两个goroutine分别启动两个服务
	g.Go(func() error {
		return server01.ListenAndServe()
	})

	g.Go(func() error {
		return server02.ListenAndServe()
	})

	if err := g.Wait(); err != nil {
		log.Fatal(err)
	}
}
```

# Cookie和Session

发布于2017/08/30 ,更新于2017/08/30 14:21:44

 

| [Golang](https://www.liwenzhou.com/categories/Golang)

 

|总阅读量：2047次



> 承蒙大家厚爱，我的《Go语言之路》的纸质版图书已经上架京东，有需要的朋友请点击 [此链接 ](https://u.jd.com/jiL06cV)购买。

Cookie和Session是Web开发绕不开的一个环节，本文介绍了Cookie和Session的原理及在Go语言中如何操作Cookie。

### Cookie

#### Cookie的由来

HTTP协议是无状态的，这就存在一个问题。

无状态的意思是每次请求都是独立的，它的执行情况和结果与前面的请求和之后的请求都无直接关系，它不会受前面的请求响应情况直接影响，也不会直接影响后面的请求响应情况。

一句有意思的话来描述就是人生只如初见，对服务器来说，每次的请求都是全新的。

状态可以理解为客户端和服务器在某次会话中产生的数据，那无状态的就以为这些数据不会被保留。会话中产生的数据又是我们需要保存的，也就是说要“保持状态”。因此Cookie就是在这样一个场景下诞生。

#### Cookie是什么

在 Internet 中，Cookie 实际上是指小量信息，是由 Web 服务器创建的，将信息存储在用户计算机上（客户端）的数据文件。一般网络用户习惯用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 Session 跟踪而存储在用户本地终端上的数据，而这些数据通常会经过加密处理。

#### Cookie的机制

Cookie是由服务器端生成，发送给User-Agent（一般是浏览器），浏览器会将Cookie的key/value保存到某个目录下的文本文件内，下次请求同一网站时就发送该Cookie给服务器（前提是浏览器设置为启用cookie）。Cookie名称和值可以由服务器端开发自己定义，这样服务器可以知道该用户是否是合法用户以及是否需要重新登录等，服务器可以设置或读取Cookies中包含信息，借此维护用户跟服务器会话中的状态。

总结一下Cookie的特点：

1. 浏览器发送请求的时候，自动把携带该站点之前存储的Cookie信息。
2. 服务端可以设置Cookie数据。
3. Cookie是针对单个域名的，不同域名之间的Cookie是独立的。
4. Cookie数据可以配置过期时间，过期的Cookie数据会被系统清除。

#### 查看Cookie

我们使用Chrome浏览器打开一个网站，打开开发者工具查看该网站保存在我们电脑上的Cookie数据。

### Go操作Cookie

#### Cookie

标准库`net/http`中定义了Cookie，它代表一个出现在HTTP响应头中Set-Cookie的值里或者HTTP请求头中Cookie的值的`HTTP cookie`。

```go
type Cookie struct {
    Name       string
    Value      string
    Path       string
    Domain     string
    Expires    time.Time
    RawExpires string
    // MaxAge=0表示未设置Max-Age属性
    // MaxAge<0表示立刻删除该cookie，等价于"Max-Age: 0"
    // MaxAge>0表示存在Max-Age属性，单位是秒
    MaxAge   int
    Secure   bool
    HttpOnly bool
    Raw      string
    Unparsed []string // 未解析的“属性-值”对的原始文本
}
```

#### 关键配置

- **Domain**: 也就是 Cookie 可以用在什么域名下，按照最小化原则来设定。
- **Path**：Cookie 可以用在什么路径下，同样按照最小化原则来设定。
- **Max-Age 和 Expires**：过期时间，只保留必要时间。
- **Http-Only**：设置为 true 的话，那么浏览器上的 JS 代码将无法使用这个 Cookie。永远设置为 true。
- **Secure**：只能用于 HTTPS 协议，生产环境永远设置为 true。
- **SameSite**：是否允许跨站发送 Cookie，尽量避免

#### 设置Cookie

`net/http`中提供了如下`SetCookie`函数，它在w的头域中添加Set-Cookie头，该HTTP头的值为cookie。

```go
func SetCookie(w ResponseWriter, cookie *Cookie)
```

#### 获取Cookie

`Request`对象拥有两个获取Cookie的方法和一个添加Cookie的方法：

获取Cookie的两种方法：

```go
// 解析并返回该请求的Cookie头设置的所有cookie
func (r *Request) Cookies() []*Cookie

// 返回请求中名为name的cookie，如果未找到该cookie会返回nil, ErrNoCookie。
func (r *Request) Cookie(name string) (*Cookie, error)
```

添加Cookie的方法：

```go
// AddCookie向请求中添加一个cookie。
func (r *Request) AddCookie(c *Cookie)
```

#### gin框架操作Cookie

```go
import (
    "fmt"

    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()
    router.GET("/cookie", func(c *gin.Context) {
        cookie, err := c.Cookie("gin_cookie") // 获取Cookie
        if err != nil {
            cookie = "NotSet"
            // 设置Cookie
            c.SetCookie("gin_cookie", "test", 3600, "/", "localhost", false, true)
        }
        fmt.Printf("Cookie value: %s \n", cookie)
    })

    router.Run()
}
```

### Session

#### Session的由来

Cookie虽然在一定程度上解决了“保持状态”的需求，但是由于Cookie本身最大支持4096字节，以及Cookie本身保存在客户端，可能被拦截或窃取，因此就需要有一种新的东西，它能支持更多的字节，并且他保存在服务器，有较高的安全性。这就是`Session`。

问题来了，基于HTTP协议的无状态特征，服务器根本就不知道访问者是“谁”。那么上述的Cookie就起到桥接的作用。

用户登陆成功之后，我们在服务端为每个用户创建一个特定的session和一个唯一的标识，它们一一对应。其中：

- Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；
- 唯一标识通常称为`Session ID`会写入用户的Cookie中。

这样该用户后续再次访问时，请求会自动携带Cookie数据（其中包含了`Session ID`），服务器通过该`Session ID`就能找到与之对应的Session数据，也就知道来的人是“谁”。

总结而言：Cookie弥补了HTTP无状态的不足，让服务器知道来的人是“谁”；但是Cookie以文本的形式保存在本地，自身安全性较差；所以我们就通过Cookie识别不同的用户，对应的在服务端为每个用户保存一个Session数据，该Session数据中能够保存具体的用户数据信息。

另外，上述所说的Cookie和Session其实是共通性的东西，不限于语言和框架。

### 练习题

1. 编写代码实现一个gin框架版Session中间件。

# swagger生成接口文档

## swagger介绍

Swagger本质上是一种用于描述使用JSON表示的RESTful API的接口描述语言。Swagger与一组开源软件工具一起使用，以设计、构建、记录和使用RESTful Web服务。Swagger包括自动文档，代码生成和测试用例生成。

在前后端分离的项目开发过程中，如果后端同学能够提供一份清晰明了的接口文档，那么就能极大地提高大家的沟通效率和开发效率。可是编写接口文档历来都是令人头痛的，而且后续接口文档的维护也十分耗费精力。

最好是有一种方案能够既满足我们输出文档的需要又能随代码的变更自动更新，而Swagger正是那种能帮我们解决接口文档问题的工具。

这里以gin框架为例，使用[gin-swagger](https://github.com/swaggo/gin-swagger)库以使用Swagger 2.0自动生成RESTful API文档。

## gin-swagger实战

想要使用`gin-swagger`为你的代码自动生成接口文档，一般需要下面三个步骤：

1. 按照swagger要求给接口代码添加声明式注释，具体参照[声明式注释格式](https://swaggo.github.io/swaggo.io/declarative_comments_format/)。
2. 使用swag工具扫描代码自动生成API接口文档数据
3. 使用gin-swagger渲染在线接口文档页面

### 第一步：添加注释

在程序入口main函数上以注释的方式写下项目相关介绍信息。

```go
package main

func main() {
	r := gin.New()

	r.Run()
}
```

在你代码中处理请求的接口函数（通常位于controller层）按如下方式写上注释：

```go
// PingExample godoc
// @Summary ping example
// @Schemes
// @Description do ping
// @Tags 首页
// @Accept json
// @Produce json
// @Success 200 {string} welcome
// @Router /index	
func GetPostListHandler2(c *gin.Context) {
	// GET请求参数(query string)：/api/v1/posts2?page=1&size=10&order=time
	// 初始化结构体时指定初始参数
	p := &models.ParamPostList{
		Page:  1,
		Size:  10,
		Order: models.OrderTime,
	}

	if err := c.ShouldBindQuery(p); err != nil {
		zap.L().Error("GetPostListHandler2 with invalid params", zap.Error(err))
		ResponseError(c, CodeInvalidParam)
		return
	}
	data, err := logic.GetPostListNew(p)
	// 获取数据
	if err != nil {
		zap.L().Error("logic.GetPostList() failed", zap.Error(err))
		ResponseError(c, CodeServerBusy)
		return
	}
	ResponseSuccess(c, data)
	// 返回响应
}
```

上面注释中参数类型使用了`object`，`models.ParamPostList`具体定义如下：

```go
// bluebell/models/params.go

// ParamPostList 获取帖子列表query string参数
type ParamPostList struct {
	CommunityID int64  `json:"community_id" form:"community_id"`   // 可以为空
	Page        int64  `json:"page" form:"page" example:"1"`       // 页码
	Size        int64  `json:"size" form:"size" example:"10"`      // 每页数据量
	Order       string `json:"order" form:"order" example:"score"` // 排序依据
}
```

响应数据类型也使用的`object`，我个人习惯在controller层专门定义一个`docs_models.go`文件来存储文档中使用的响应数据model。

```go
// bluebell/controller/docs_models.go

// _ResponsePostList 帖子列表接口响应数据
type _ResponsePostList struct {
	Code    ResCode                 `json:"code"`    // 业务响应状态码
	Message string                  `json:"message"` // 提示信息
	Data    []*models.ApiPostDetail `json:"data"`    // 数据
}
```

### 第二步：生成接口文档数据

编写完注释后，使用以下命令安装swag工具：

```bash
go get -u github.com/swaggo/swag/cmd/swag
```

在项目根目录执行以下命令，使用swag工具生成接口文档数据。

```bash
swag init
```

执行完上述命令后，如果你写的注释格式没问题，此时你的项目根目录下会多出一个`docs`文件夹。

```bash
./docs
├── docs.go
├── swagger.json
└── swagger.yaml
```

### 第三步：引入gin-swagger渲染文档数据

然后在项目代码中注册路由的地方按如下方式引入`gin-swagger`相关内容：

```go
import (
	// liwenzhou.com ...

	_ "bluebell/docs"  // 千万不要忘了导入把你上一步生成的docs
	swaggerFiles "github.com/swaggo/files"
	gs "github.com/swaggo/gin-swagger"
    
	"github.com/gin-gonic/gin"
)
```

注册swagger api相关路由

```go
r.GET("/swagger/*any", gs.WrapHandler(swaggerFiles.Handler))
```

把你的项目程序运行起来，打开浏览器访问http://localhost:8080/swagger/index.html就能看到Swagger 2.0 Api文档了。

`gin-swagger`同时还提供了`DisablingWrapHandler`函数，方便我们通过设置某些环境变量来禁用Swagger。例如：

```go
r.GET("/swagger/*any", gs.DisablingWrapHandler(swaggerFiles.Handler, "NAME_OF_ENV_VARIABLE"))
```

此时如果将环境变量`NAME_OF_ENV_VARIABLE`设置为任意值，则`/swagger/*any`将返回404响应，就像未指定路由时一样。

# 优雅地关机或重启

## 优雅地关机

### 什么是优雅关机？

优雅关机就是服务端关机命令发出后不是立即关机，而是等待当前还在处理的请求全部处理完毕后再退出程序，是一种对客户端友好的关机方式。而执行`Ctrl+C`关闭服务端时，会强制结束进程导致正在访问的请求出现问题。

### 如何实现优雅关机？

Go 1.8版本之后， http.Server 内置的 [Shutdown()](https://golang.org/pkg/net/http/#Server.Shutdown) 方法就支持优雅地关机，具体示例如下：

```go
// +build go1.8

package main

import (
	"context"
	"log"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"time"

	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default()
	router.GET("/", func(c *gin.Context) {
		time.Sleep(5 * time.Second)
		c.String(http.StatusOK, "Welcome Gin Server")
	})

	srv := &http.Server{
		Addr:    ":8080",
		Handler: router,
	}

	go func() {
		// 开启一个goroutine启动服务
		if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
			log.Fatalf("listen: %s\n", err)
		}
	}()

	// 等待中断信号来优雅地关闭服务器，为关闭服务器操作设置一个5秒的超时
	quit := make(chan os.Signal, 1) // 创建一个接收信号的通道
	// kill 默认会发送 syscall.SIGTERM 信号
	// kill -2 发送 syscall.SIGINT 信号，我们常用的Ctrl+C就是触发系统SIGINT信号
	// kill -9 发送 syscall.SIGKILL 信号，但是不能被捕获，所以不需要添加它
	// signal.Notify把收到的 syscall.SIGINT或syscall.SIGTERM 信号转发给quit
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)  // 此处不会阻塞
	<-quit  // 阻塞在此，当接收到上述两种信号时才会往下执行
	log.Println("Shutdown Server ...")
	// 创建一个5秒超时的context
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	// 5秒内优雅关闭服务（将未处理完的请求处理完再关闭服务），超过5秒就超时退出
	if err := srv.Shutdown(ctx); err != nil {
		log.Fatal("Server Shutdown: ", err)
	}

	log.Println("Server exiting")
}
```

如何验证优雅关机的效果呢？

上面的代码运行后会在本地的`8080`端口开启一个web服务，它只注册了一条路由`/`，后端服务会先sleep 5秒钟然后才返回响应信息。

我们按下`Ctrl+C`时会发送`syscall.SIGINT`来通知程序优雅关机，具体做法如下：

1. 打开终端，编译并执行上面的代码
2. 打开一个浏览器，访问`127.0.0.1:8080/`，此时浏览器白屏等待服务端返回响应。
3. 在终端**迅速**执行`Ctrl+C`命令给程序发送`syscall.SIGINT`信号
4. 此时程序并不立即退出而是等我们第2步的响应返回之后再退出，从而实现优雅关机。

### 优雅地重启

优雅关机实现了，那么该如何实现优雅重启呢？

我们可以使用 [fvbock/endless](https://github.com/fvbock/endless) 来替换默认的 `ListenAndServe`启动服务来实现， 示例代码如下：

```go
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/fvbock/endless"
	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default()
	router.GET("/", func(c *gin.Context) {
		time.Sleep(5 * time.Second)
		c.String(http.StatusOK, "hello gin!")
	})
	// 默认endless服务器会监听下列信号：
	// syscall.SIGHUP，syscall.SIGUSR1，syscall.SIGUSR2，syscall.SIGINT，syscall.SIGTERM和syscall.SIGTSTP
	// 接收到 SIGHUP 信号将触发`fork/restart` 实现优雅重启（kill -1 pid会发送SIGHUP信号）
	// 接收到 syscall.SIGINT或syscall.SIGTERM 信号将触发优雅关机
	// 接收到 SIGUSR2 信号将触发HammerTime
	// SIGUSR1 和 SIGTSTP 被用来触发一些用户自定义的hook函数
	if err := endless.ListenAndServe(":8080", router); err!=nil{
		log.Fatalf("listen: %s\n", err)
	}

	log.Println("Server exiting")
}
```

如何验证优雅重启的效果呢？

我们通过执行`kill -1 pid`命令发送`syscall.SIGINT`来通知程序优雅重启，具体做法如下：

1. 打开终端，`go build -o graceful_restart`编译并执行`./graceful_restart`,终端输出当前pid(假设为43682)
2. 将代码中处理请求函数返回的`hello gin!`修改为`hello q1mi!`，再次编译`go build -o graceful_restart`
3. 打开一个浏览器，访问`127.0.0.1:8080/`，此时浏览器白屏等待服务端返回响应。
4. 在终端**迅速**执行`kill -1 43682`命令给程序发送`syscall.SIGHUP`信号
5. 等第3步浏览器收到响应信息`hello gin!`后再次访问`127.0.0.1:8080/`会收到`hello q1mi!`的响应。
6. 在不影响当前未处理完请求的同时完成了程序代码的替换，实现了优雅重启。

但是需要注意的是，此时程序的PID变化了，因为`endless` 是通过`fork`子进程处理新请求，待原进程处理完当前请求后再退出的方式实现优雅重启的。所以当你的项目是使用类似`supervisor`的软件管理进程时就**不适用**这种方式了。

## 总结

无论是优雅关机还是优雅重启归根结底都是通过监听特定系统信号，然后执行一定的逻辑处理保障当前系统正在处理的请求被正常处理后再关闭当前进程。使用优雅关机还是使用优雅重启以及怎么实现，这就需要根据项目实际情况来决定了。

# 在gin框架中使用JWT

JWT全称JSON Web Token是一种跨域认证解决方案，属于一个开放的标准，它规定了一种Token实现方式，目前多用于前后端分离项目和OAuth2.0业务场景下。

## 什么是JWT？

JWT全称JSON Web Token是一种跨域认证解决方案，属于一个开放的标准，它规定了一种Token 实现方式，目前多用于前后端分离项目和 OAuth2.0 业务场景下。

## 为什么需要JWT？

在之前的一些web项目中，我们通常使用的是`Cookie-Session`模式实现用户认证。相关流程大致如下：

1. 用户在浏览器端填写用户名和密码，并发送给服务端
2. 服务端对用户名和密码校验通过后会生成一份保存当前用户相关信息的session数据和一个与之对应的标识（通常称为session_id）
3. 服务端返回响应时将上一步的session_id写入用户浏览器的Cookie
4. 后续用户来自该浏览器的每次请求都会自动携带包含session_id的Cookie
5. 服务端通过请求中的session_id就能找到之前保存的该用户那份session数据，从而获取该用户的相关信息。

这种方案依赖于客户端（浏览器）保存 Cookie，并且需要在服务端存储用户的session数据。

在移动互联网时代，我们的用户可能使用浏览器也可能使用APP来访问我们的服务，我们的web应用可能是前后端分开部署在不同的端口，有时候我们还需要支持第三方登录，这下`Cookie-Session`的模式就有些力不从心了。

JWT就是一种基于Token的轻量级认证模式，服务端认证通过后，会生成一个JSON对象，经过签名后得到一个Token（令牌）再发回给用户，用户后续请求只需要带上这个Token，服务端解密之后就能获取该用户的相关信息了。

想要了解JWT的原理，推荐大家阅读：[阮一峰的JWT入门教程](https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)

## 安装

我们使用 Go 语言社区中的 jwt 相关库来构建我们的应用，例如：https://github.com/golang-jwt/jwt。

```bash
go get github.com/golang-jwt/jwt/v4
```

本文将使用这个库来实现我们生成JWT和解析JWT的功能。

## 使用

### 默认Claim

如果我们直接使用JWT中默认的字段，没有其他定制化的需求则可以直接使用这个包中的和方法快速生成和解析token。

```go
// 用于签名的字符串
var mySigningKey = []byte("liwenzhou.com")

// GenRegisteredClaims 使用默认声明创建jwt
func GenRegisteredClaims() (string, error) {
	// 创建 Claims
	claims := &jwt.RegisteredClaims{
		ExpiresAt: jwt.NewNumericDate(time.Now().Add(time.Hour * 24)), // 过期时间
		Issuer:    "qimi",                                             // 签发人
	}
	// 生成token对象
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	// 生成签名字符串
	return token.SignedString(mySigningKey)
}

// ParseRegisteredClaims 解析jwt
func ValidateRegisteredClaims(tokenString string) bool {
	// 解析token
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
		return mySigningKey, nil
	})
	if err != nil { // 解析token失败
		return false
	}
	return token.Valid
}
```

### 自定义Claims

我们需要定制自己的需求来决定JWT中保存哪些数据，比如我们规定在JWT中要存储`username`信息，那么我们就定义一个`MyClaims`结构体如下：

```go
// CustomClaims 自定义声明类型 并内嵌jwt.RegisteredClaims
// jwt包自带的jwt.RegisteredClaims只包含了官方字段
// 假设我们这里需要额外记录一个username字段，所以要自定义结构体
// 如果想要保存更多信息，都可以添加到这个结构体中
type CustomClaims struct {
	// 可根据需要自行添加字段
	Username             string `json:"username"`
	jwt.RegisteredClaims        // 内嵌标准的声明
}
```

然后我们定义JWT的过期时间，这里以24小时为例：

```go
const TokenExpireDuration = time.Hour * 24
```

接下来还需要定义一个用于签名的字符串：

```go
// CustomSecret 用于加盐的字符串
var CustomSecret = []byte("夏天夏天悄悄过去")
```

### 生成JWT

我们可以根据自己的业务需要封装一个生成 token 的函数。

```go
// GenToken 生成JWT
func GenToken(username string) (string, error) {
	// 创建一个我们自己的声明
	claims := CustomClaims{
		username, // 自定义字段
		jwt.RegisteredClaims{
			ExpiresAt: jwt.NewNumericDate(time.Now().Add(TokenExpireDuration)),
			Issuer:    "my-project", // 签发人
		},
	}
	// 使用指定的签名方法创建签名对象
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	// 使用指定的secret签名并获得完整的编码后的字符串token
	return token.SignedString(CustomSecret)
}
```

### 解析JWT

根据给定的 JWT 字符串，解析出数据。

```go
// ParseToken 解析JWT
func ParseToken(tokenString string) (*CustomClaims, error) {
	// 解析token
	// 如果是自定义Claim结构体则需要使用 ParseWithClaims 方法
	token, err := jwt.ParseWithClaims(tokenString, &CustomClaims{}, func(token *jwt.Token) (i interface{}, err error) {
		// 直接使用标准的Claim则可以直接使用Parse方法
		//token, err := jwt.Parse(tokenString, func(token *jwt.Token) (i interface{}, err error) {
		return CustomSecret, nil
	})
	if err != nil {
		return nil, err
	}
	// 对token对象中的Claim进行类型断言
	if claims, ok := token.Claims.(*CustomClaims); ok && token.Valid { // 校验token
		return claims, nil
	}
	return nil, errors.New("invalid token")
}
```

## 在gin框架中使用JWT

首先我们注册一条路由`/auth`，对外提供获取Token的渠道：

```go
r.POST("/auth", authHandler)
```

我们的`authHandler`定义如下：

```go
func authHandler(c *gin.Context) {
	// 用户发送用户名和密码过来
	var user UserInfo
	err := c.ShouldBind(&user)
	if err != nil {
		c.JSON(http.StatusOK, gin.H{
			"code": 2001,
			"msg":  "无效的参数",
		})
		return
	}
	// 校验用户名和密码是否正确
	if user.Username == "q1mi" && user.Password == "q1mi123" {
		// 生成Token
		tokenString, _ := GenToken(user.Username)
		c.JSON(http.StatusOK, gin.H{
			"code": 2000,
			"msg":  "success",
			"data": gin.H{"token": tokenString},
		})
		return
	}
	c.JSON(http.StatusOK, gin.H{
		"code": 2002,
		"msg":  "鉴权失败",
	})
	return
}
```

用户通过上面的接口获取Token之后，后续就会携带着Token再来请求我们的其他接口，这个时候就需要对这些请求的Token进行校验操作了，很显然我们应该实现一个检验Token的中间件，具体实现如下：

```go
// JWTAuthMiddleware 基于JWT的认证中间件
func JWTAuthMiddleware() func(c *gin.Context) {
	return func(c *gin.Context) {
		// 客户端携带Token有三种方式 1.放在请求头 2.放在请求体 3.放在URI
		// 这里假设Token放在Header的Authorization中，并使用Bearer开头
		// 这里的具体实现方式要依据你的实际业务情况决定
		authHeader := c.Request.Header.Get("Authorization")
		if authHeader == "" {
			c.JSON(http.StatusOK, gin.H{
				"code": 2003,
				"msg":  "请求头中auth为空",
			})
			c.Abort()
			return
		}
		// 按空格分割
		parts := strings.SplitN(authHeader, " ", 2)
		if !(len(parts) == 2 && parts[0] == "Bearer") {
			c.JSON(http.StatusOK, gin.H{
				"code": 2004,
				"msg":  "请求头中auth格式有误",
			})
			c.Abort()
			return
		}
		// parts[1]是获取到的tokenString，我们使用之前定义好的解析JWT的函数来解析它
		mc, err := ParseToken(parts[1])
		if err != nil {
			c.JSON(http.StatusOK, gin.H{
				"code": 2005,
				"msg":  "无效的Token",
			})
			c.Abort()
			return
		}
		// 将当前请求的username信息保存到请求的上下文c上
		c.Set("username", mc.Username)
		c.Next() // 后续的处理函数可以用过c.Get("username")来获取当前请求的用户信息
	}
}
```

注册一个`/home`路由，发个请求验证一下吧。

```go
r.GET("/home", JWTAuthMiddleware(), homeHandler)

func homeHandler(c *gin.Context) {
	username := c.MustGet("username").(string)
	c.JSON(http.StatusOK, gin.H{
		"code": 2000,
		"msg":  "success",
		"data": gin.H{"username": username},
	})
}
```

如果不想自己实现上述功能，你也可以使用Github上别人封装好的包，比如https://github.com/appleboy/gin-jwt。

### refresh token

在某些业务场景下，我们可能还需要使用refresh token。

这里可以参考 [RFC 6749 OAuth2.0中关于refresh token的介绍](https://datatracker.ietf.org/doc/html/rfc6749#section-1.5)

