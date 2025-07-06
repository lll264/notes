# 环境配置

# go依赖

https://blog.csdn.net/Sihang_Xie/article/details/124851399

## go依赖演进

### GOPATH

最开始的时候就是使用GOPATH管理依赖，gopath分出了三个目录

| 文件夹 | 作用                 |
| ------ | -------------------- |
| bin    | 项目编译的二进制文件 |
| pkg    | 项目编译的中间产物   |
| src    | 项目源码             |

项目代码直接依赖 src 下的代码，go get 下载最新版本的包到 src 目录下。

这样有个弊端就是，**src 下只能允许一个版本的依赖存在，如果不同项目需要依赖的不同版本时会发生冲突**

### GO Vendor

简单来说就是在每个项目中增加了一个vendor目录存放项目需要的依赖，这样就不会发生冲突了。

 Vendor 不能很清晰地标识依赖的版本概念。

### Go Module

Go Module 是 Go 语言官方推出的依赖管理系统，解决了之前依赖管理系统存在的诸如无法依赖同一个库的多个版本等问题

# 基础

### 一、初步介绍

#### 1.go语言基础：

- 包声明，编写源文件时，必须在非注释的第一行指明这个文件属于哪个包，如package main。
- 引入包，其实就是告诉Go 编译器这个程序需要使用的包，如import "fmt"其实就是引入了fmt包。
- 函数，和c语言相同，即是一个可以实现某一个功能的函数体，每一个可执行程序中必须拥有一个main函数。
- 变量，Go 语言变量名由字母、数字、下划线组成，其中首个字符不能为数字。
- 语句/表达式，==在Go程序中，一行代表一个语句结束。每个语句不需要像 C 家族中的其它语言一样以分号;结尾，==因为这些工作都将由 Go 编译器自动完成。
- 注释，和c语言中的注释方式相同
- ，可以在任何地方使用以 // 开头的单行注释。以 /* 开头，并以 */ 结尾来进行多行注释，且不可以嵌套使用，多行注释一般用于包的文档描述或注释成块的代码片段。
- ==一个变量分配在堆上还是栈上，与语法无关，主要依靠golang的逃逸分析，所以很多内存问题不必担心==

##### 1.1 输入输出

```go
fmt.Println() 	//打印并换行
fmt.Printf() 	//格式化输出
fmt.Print()		//打印输出

//遇空格或回车结束
fmt.Scanf()
fmt.Scanln()
fmt.Scan()
```

- `fmt.Scanln`函数在读取输入时，会检查输入的格式，以确保在读取完所有指定的参数后没有多余的输入，否则会返回错误
- Scanf是格式化输入
- Scan没有输入末尾的格式检验

Println输出可以接收多个变量，或者直接输出数组

```go
a := 1
b := 2
arr := [3]int{1,2,3}
println(a, b)// 1 2
println(arr) //1 2 3
```

格式化输出

```go
%v     仅输出该变量的值
%T	   输出变量类型
%d	   整数	
%.1f   保留一位小数输出浮点数
%s	   字符串
%c	   字符
%p	   地址
```

#### 2.变量

##### 2.1 变量定义

变量类型总是放在变量名后⾯面，但可以省略 (根据初始化值进⾏行类型推断)

```go
package main
import "fmt"
func main() {
    //var(关键字) 变量名 变量类型
	var a int = 27
    //省略变量类型
    var b = "hello"
    //使用:=
    c := "123"
    //同时声明多个类型相同的变量
    var a, b int 
    var x, y = 1, "sdf"
    c, d := 1, 2
    //可以用括号括起来
    var (
    	a int
        b int
    )
	fmt.Println(a, b, c);
} 
```

指定变量类型，==如果没有初始化，则变量默认为零值。==**零值就是变量没有做初始化时系统默认设置的值**。
• bool: false
• integers: 0
• floats: 0.0
• string: ""
• pointers, functions, interfaces, slices, channels, maps: nil

##### 2.2 匿名变量

匿名变量的特点是一个下画线_，这本身就是一个特殊的标识符，被称为空白标识符。它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。

```go
//使用匿名变量时，只需要在变量声明的地方使用下画线替换即可。
func GetData() (int, int) {
    return 10, 20
}
func main(){
    a, _ := GetData()
    _, b := GetData()
    fmt.Println(a, b)
}
```

需要注意的是匿名变量不占用内存空间，不会分配内存。**匿名变量与匿名变量之间也不会因为多次声明而无法使用。**

##### 2.3 变量作用域

作用域指的是已声明的标识符所表示的常量、类型、函数或者包在源代码中的作用范围，在此我们主要看一下go中变量的作用域，根据变量定义位置的不同，可以分为一下三个类型：

- 函数内定义的变量为局部变量，这种局部变量的作用域只在函数体内，函数的参数和返回值变量都属于局部变量。这种变量在存在于函数被调用时，销毁于函数调用结束后。
- 函数外定义的变量为全局变量，全局变量只需要在一个源文件中定义，就可以在所有源文件中使用，甚至可以使用import引入外部包来使用。全局变量声明必须以 var 关键字开头，**如果想要在外部包中使用全局变量的首字母必须大写。**
- 函数定义中的变量成为形式参数，定义函数时函数名后面括号中的变量叫做形式参数（简称形参）。形式参数只在函数调用时才会生效，函数调用结束后就会被销毁，在函数未被调用时，函数的形参并不占用实际的存储单元，也没有实际值。形式参数会作为函数的局部变量来使用。

##### 2.4 注意

- ==编译器会认为一个未被使用的变量和导入包是个错误。==
- 在函数内部，甚⾄至可以省略var关键字，**用:=这种更短的表达式完成变量类型推断和初始化**。==函数外部不能使用赋值语句，故不能使用==

```go
a := 1 //等价入戏
var a int
a = 1; //赋值语句
```



#### 3.常量

##### 3.1 使用

使用const关键字，必须初始化且不能被修改

```go
const x uint32 = 123
const y = "Hello"
const a, b, c = "meat", 2, "veg" // 同样⽀支持⼀一次定义多个常量。
const (
z = false
a = 123
)
```

在定义常量组时，如不提供初始化值，**则表⽰示与上⾏行常量的类型、值表达式完全相同** (是表达式相同⽽而⾮非结果相同)。

```go
func main() {
    const (
        a = "abc"
        b
    )
    println(a, b)	//abc abc
}
```

常量值还可以是 len()、cap()，unsafe.Sizeof() 常量计算表达式的值。

```go
const (
    a = "abc"
    b = len(a)
    c = unsafe.Sizeof(a)
)
```

如果目标类型⾜足以存储常量值，不会导致溢出 (overflow)，可不做显式转换。

```go
var million int = 1e6  	// float syntax OK here
var b byte = 'a' 		// int to byte
```

##### 3.2 枚举

可以⽤用 iota ⽣生成从 0 开始的⾃自动增⻓长枚举值。按⾏行递增，可以省略后续⾏行的 iota 关键字。`后续行变量都为当前的iota值，主动赋值后当前和后面的变量就都变为了赋的值`

```go
const (
    Sunday = iota 	// 0
    Monday  		// 1
    Tuesday  		// 2
    Wednesday  		// 3
)
const (
	a = iota 		//0
    b				//1
    c = "haha"		//haha   iota 2
    d				//haha 	 iota 3
    e = iota		//4
)
```



#### 4.基本数据类型

| 类型                             | 描述                                            |
| -------------------------------- | ----------------------------------------------- |
| bool                             | true,false。默认false,不能把⾮非零值当作 true。 |
| 字符串                           | string                                          |
| uint8 / uint16 / uint32 / uint64 | 无符号 8 / 16 / 32 / 64位整型                   |
| int8 / int16 / int32 / int64     | 有符号8 / 16 / 32 / 64位整型                    |
| float32 / float64                | IEEE-754 32 / 64 位浮点型数                     |
| complex64 / complex128           | 32 / 64 位实数和虚数                            |
| byte                             | 类似 uint8                                      |
| rune                             | 类似 int32                                      |
| uintptr                          | 无符号整型，用于存放一个指针                    |

Go 语言变量名由字母、数字、下划线组成，其中首个字符不能为数字。

可以⽤用 ⼋八进制 (071)、⼗十六进制 (0xFF) 或 科学计数法 (1e2) 对整数类型进⾏行赋值。math 包中包含了 Min/Max 开头的各类型最小、最大值常量。

##### 4.1 类型转换

不支持隐式转换 ，必须进⾏行显式类型转换 "<type>(expression)"。

```go
a := 3
b := 5.0
c := float64(a)
d := int(b)
e := []byte("sdf");
```

类型转换只能在取值范围较小的类型转换到取值范围较大的类型，当取值范围较大的转换为取值范围较小的类型时会发生精度丢失。

#### 5.运算符

==Go运算符都是从左到右结合==

算术运算符：+ - * / % ++ --

关系运算符：== !=  >  <  >= <=

逻辑运算符：&& || ！

位运算符：& | ^ &^ << >>

赋值运算符：= += ...

#### 6.流程控制

##### 6.1 if

if语句只需记住：
• 条件表达式没有括号；
• ⽀持⼀个初始化表达式 (可以是多变量初始化语句)；
• 左大括号必须和条件语句在同一行, 不然会编译错误。

```go
func main() {
    a := 10
    if a > 0 { 	//左大括号必须写在这，否则被解释为 "if a > 0;"导致编译出错。
    	a += 100
    } else if a == 0 { // 注意左大括号位置。
    	a = 0
    } else { 		// 注意左大括号位置。
    	a -= 100
    }
    println(a)
}
```

##### 6.2 switch

switch case 表达式可以使⽤用任意类型或表达式。也不必写 break，⾃自动终⽌止。如希望继续下⼀case 处理，则须显式执行fallthrough。

```go
switch score {
    case 90:
    	fmt.println("123")
	case 79:
        a := 10
        fallthrough
    case 80:
    default:
}
//默认是有一个true或false的匹配，且默认是去匹配true
switch {
    case true:
    case false:
}
```

##### 6.3 for

没有while循环，for有三种形式

```go
for i:=0; i<10; i++ {}
for i < 10 {}
for {}	//默认true一直循环
```

###### for range不要对迭代参数取地址

```go
func main() {
   a := []int{1, 2, 3}
   arr := make([]*int, 3)
   for k, v := range a {
      //不要对迭代参数取地址，因为k, v是切片元素的副本，而不是本身的元素
      arr[k] = &v
   }
   for i := 0; i < 3; i++ {
      fmt.Println(*arr[i]) //是副本元素
   }
}
```

##### 6.4 break continue

同c/c++

#### 7.指针

与C相同，取地址符同样是&，指针变量其实就是用于存放某一个对象的内存地址。

##### 7.1 指针声明和初始化

和基础类型数据相同，在使用指针变量之前我们首先需要申明指针，声明格式如下：

```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */
```

指针的初始化就是取出相对应的变量地址对指针进行赋值，具体如下：

```go
var a int = 20   /* 声明实际变量 */
var ip *int        /* 声明指针变量 */
ip = &a  /* 指针变量的存储地址 */
```

##### 7.2 空指针

当一个指针被定义后没有分配到任何变量时，它的值为==nil==，也称为空指针。它概念上和其它语言的null、NULL一样，都指代零值或空值。

#### 8.字符串

string是不可变值类型，Go的字符串是由单个字节连接起来的。Go语言的字符串的字节使用 UTF-8 编码标识 Unicode文本

##### 8.1 注意

- 字符串一旦赋值了，字符串就不能修改了
- 字符串拼接直接用‘+’
- 可以⽤用索引号访问某个字节，如 s[i]。
- 不能通过序号获取字节元素指针，&s[i] 是⾮非法的。

##### 8.2 遍历方法

```go
for i := 0; i < len(s); i++ {
    fmt.Printf("%c", s[i])
}
//快捷输入 forr
for i, v := range str {
    fmt.Print(i)
    fmt.Printf("%c", v)
}
```



### 二、函数、包和错误处理

- 函数是基本的代码块，用于执行一个任务
- Go语言最少有个main函数
- **不支持重载和默认参数**

#### 1.函数定义和使用

- fun 函数名(参数，参数...) 返回值类型 {函数体}
- 如果函数返回值只有一个可以不写()
- 函数返回值可以有多个，在接收是，如果希望忽略某个返回值可以**使用匿名变量_占位**

```go
func add(a, b int) int {
    c := a + b
    return c
}
//可以有多个返回值
func f(a int, b int) (int, int) {
    return 1, 2
}
a, b := f(1, 2)
//可以自动返回参数
func f() (a int, b int) {
    a = 10
    b = 20
    return
}
//可变参数，必须要是最后一个参数, 一个函数最多只有一个可变参数
//有可变参数后不能有其他参数
func f(a int, nums ...int) int {
    var res int = 0;
    for i := 0; i < len(nums); i++ {
        res += nums[i]
    }
    return res
}
f(1,2,3,4)	//9
var arr = []int{1,2,3}
f(1, arr...)
```

#### 2.包

##### 2.1 基本概念

go的每一个文件都是属于一个包的，也就是说go是以包的形式来管理文件和项目目录结构
的。通过包可以区分相同名字的函数、变量等标识符，当程序文件很多时,可以很好的管理项目控制函数、变量等访问范围，即作用域。

##### 2.2 使用

打包	package 包名

引入包	import"包的路径"  
import"project1/..."
import (
"包名"
"包名"
别名 “包名”
)

访问其他包函数或变量	包名.函数/变量

##### 2.3 注意与细节

- package 指令在 文件第一行，然后是 import 指令。
- ==一个程序中一个函数或变量要被其他包使用时，名称首字母要大写，类似public==
- 在 import 包时，路径从 $GOPATH  的 src 下开始，不用带 src , 编译器会自动从 src 下开始引入
- 如果包名较长，Go支持给包取别名(直接在包名前面写)，**取别名后，原来的包名就不能使用了**
- 如果你要编译成一个可执行程序文件，就需要将这个包声明为main, 即package main，这是一个语法规范，如果你是写一个库，包名可以自定义

#### 3.init函数

##### 3.1 介绍

**每一个源文件都可以包含一个** **init** **函数**，该函数会在 main 函数执行前，被 Go 运行框架调用，也就是说 init 会在 main 函数前被调用。

##### 3.2 注意

- 如果一个文件同时包含全局变量定义，init函数和main函数，则执行的流程为
  **全局变量定义->init函数->main函数**
- init 函数最主要的作用，就是完成一些**初始化的工作**，类似java静态代码块
- 如果两个包都含有全局变量、init函数、则执行流程如下
  <img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240229170523736.png" alt="image-20240229170523736" style="zoom: 80%;" />

#### 4.匿名函数

**在函数内部不能定义函数，但匿名函数可以**，匿名函数就是没有名字的函数，如果我们某个函数只是希望使用一次，可以考虑使用匿名函数，匿名函数也可以实现多次调用。

##### 4.1 使用

```go
func main() {
    //在定义匿名函数是就直接调用
    res1 := func(a, b int) int {
        return a + b
    }(1,2)
}
```

##### 4.2 函数变量

函数其实也是一个变量，其类型为==func(参数类型) 返回值==

```go
//将匿名函数赋给一个变量
a := func(a, b int) int {
    return a + b
}
res1 = a(1, 2)
res2 = a(2, 3)
```

#### 5.闭包

闭包就是**一个匿名函数**和与**其相关的引用环境**组合的**一个整体**(实体)

##### 5.1 认识其结构

```go
//累加器
func AddUpper() func(int)int {
	var n int = 10
	return func (x int) int {
		n = n + x
		return n
	}
}

func main() {
	f := AddUpper()
	fmt.Println(f(1)) //n=11
	fmt.Println(f(1)) //n=12
	fmt.Println(f(1)) //n=13
}
```

可以看到，main函数中的变量在接收到AddUpper返回的匿名函数后，f调用时操作了AddUpper函数中的n，每次对n的修改都会保留。**类似面向对象中的类。**

==闭包就是返回的函数和操作的外部函数变量构成的==

##### 5.2 案例

编写一个函数 makeSuffix(suffix string),可以接收一个文件后缀名(比如.jpg)，并返回一个闭包。调用闭包，可以传入一个文件名，如果该文件名没有指定的后缀(比如.jpg) ,则返回 文件名.jpg , 如果已经有.jpg 后缀，则返回原文件名。

```go
func makeSuffix(suffix string) func(string)string {
    return func (name string) string {
        if !strings.HasSuffix(name, suffix) {
            return name + suffix
        }
        return name
    }
}

func main() {
    f := makeSuffix(".jpg")
    fmt.Println(f("winter")) //winter.jpg
    fmt.Println(f("bird.jpg"))//bird
}
```

返回的匿名函数和 makeSuffix (suffix string)的 suffix 变量 组合成一个闭包。如果使用传统的方法，也可以轻松实现这个功能，但是传统方法需要每次都传入 后缀名，比如 .jpg ,而闭包因为可以保留上次引用的某个值，所以我们传入一次就可以反复使用

#### 6.函数的defer

在函数中，程序员经常需要创建资源(比如：数据库连接、文件句柄、锁等) ，为了在**函数执行完毕后，及时的释放资源**，Go 的设计者提供 defer (延时机制)。

##### 6.1 案例

```go
func sum(a, b int) int {
    defer fmt.Println(a) //3.a=1
    defer fmt.Println(b) //2.b=2
    
    res := a + b
    fmt.Println(res) //1.res=3
    return res
}
func main() {
    res := sum(1, 2)
    fmt.Println(res) //4.res=3
}
```

##### 6.2 注意

- 当函数执行到defer时，暂不执行，会将defer后面的语句压入defer栈中
- 当函数执行完毕后，在从defer栈按照先入后出的顺序执行在 defer 将语句放入到栈时，也会将相关的值拷贝同时入栈
- 在 defer 将语句放入到栈时，也会将相关的**值拷贝**同时入栈，其值不会改变。
- ==defer后的语句必须是函数==

```go
func sum(a, b int) int {
    defer fmt.Println(a) //3.a=1
    defer fmt.Println(b) //2.b=2
 	//增加一句话
    a++ //2
    b++ //3
    res := a + b	//5
    fmt.Println(res) //1.res=3
    return res
}
func main() {
    res := sum(1, 2)
    fmt.Println(res) //4.res=3
}
```

##### 6.3 defer的最佳实践

```go
func test() {
    //关闭文件资源
    file = openfile(文件名)
    defer file.close()
   	//其他代码
}
func test() {
    //释放数据库资源
    connect = openDatabase()
    defer connect.close()
    //其他代码
}
```

这种机制，非常简洁，程序员不用再为在什么时机关闭资源而烦心。

##### defer与闭包

```go
func f1() { 
	for i := 0; i < 10; i++ {
		defer func() {
			println(i)
		}()
	}
}
func f2() {
	for i := 0; i < 10; i++ {
		defer func(val int) {
			println(val)
		}(i)
	}
}
func f3() {
	for i := 0; i < 10; i++ {
		j := i
		defer func() {
			println(j)
		}()
	}
}
```

闭包里面的函数引用的外面的变量不会被GC回收，但函数里的局部变量会。defer是执行的时候才会去确定值

上面三个分别输出：

10 10 10 10 10 。。。

9 8 7 6 5 4 。。。

9 8 7 6 5 4 。。。

#### 7.值传递和引用传递

值传递的参数是基本数据类型**int系列、float系列、bool、string、数组和结构体**

引用传递的参数是**指针、slice切片、map、管道chan、interface等**

特点：...

#### 8.错误处理

在默认情况下，当发生错误后(panic) ,程序就会退出（崩溃）。如果我们希望：当发生错误后，可以捕获到错误，并进行处理，保证程序可以继续执行。还可以在捕获到错误后，给管理员一个提示(邮件,短信。。。）

##### 8.1 基本说明

Go 语言追求简洁优雅，所以，Go 语言不支持传统的 try…catch…finally 这种处理。Go 中引入的处理方式为：**defer**, **panic**, **recover**

```go
func test() {
    //使用defer+recover来捕获异常
    defer func() {
        err := recover() //recover()是内置函数，可以捕获到异常
        if err != nil {
            fim.Println("err=", err)
            //这里可以发送错误信息给管理员
            ...
        }
    }()
    a := 10
    b := 0
    res := a/b
    fmt.Println("res=", res)
}
func main() {
    test()
    fmt.Println("sdfdsdf")
}
```

##### 8.2 自定义错误

Go 程序中，也支持自定义错误， 使用 errors.New 和 panic 内置函数。

- ==errors.New("错误说明")==,会返回一个**error类型**的值，表示一个错误
- ==panic内置函数==,接收一个interface{}类型的值（也就是任何值）作为参数。可以接收 error 类型的变量，**输出错误信息**，**并退出程序**.

```go
// 函数去读取配置文件init.conf的信息
// 如果文件名传入不正确，我们就返回一个自定义的错误
func readConf(name string) error {
	if name == "config.ini" {
		//读取...
		return nil
	} else {
		//返回一个自定义错误
		return errors.New("读取文件错误...")
	}
}

func main() {
	err := readConf("config2.ini")
	if err != nil {
		//如果读取文件错误，就输出这个错误，并终止程序
		panic(err)
	}
	fmt.Println("继续执行")
}
```



### 三、常用库函数

#### 1.字符串常用函数

(1)len：按字节统计字符串的长度

```go
s := "北京"
s2 := "err"
fmt.Println(len(s))  //6
fmt.Println(len(s2)) //3
```

(2)按字符统计字符串的长度：r := []rune(s)

```go
s := "北京"
r := []rune(s)
for i := 0; i < len(r); i++ {
    fmt.Printf("%c", r[i])
}
```

(3)字符串转整数：n, err := strconv.Atoi("12")

```go
//需导包 strconv
n, err := strconv.Atoi("hello")
if err != nil {
    fmt.Println("转换错误", err)
} else {
    fmt.Println("转成的结果是"， n)
}
```

(4)整数转字符串：str = strconv.Itoa

```go
str = strconv.Itoa(12345)
fmt.Printf("%v", str)
```

(5)字符串转[]byte：var arr = []byte("hello go")

```go
var bytes = []byte("hello go")
fmt.Print(bytes)
```

(6)[]byte转字符串：str = string([]byte{97,98,99})

```go
str = string([]byte{97,98,99})
fmt.Printf("%v", str)
```

(7)10进制转2,8,16进制： str = strconv.FormatInt(123,2) //2->8, 16

```go
str = strconv.FormatInt(123, 2)
fmt.Printf("123对应的二进制是%v", str)
str = strconv.FormatInt(123, 16)
fmt.Printf("123对应但是16进制是%v", str)
```

(8)查找子串是否在指定的字符串中：strings.Contains("seafood", "food")

```go
fmt.Println(strings.Contains("seafood", "food")) //true
```

(9)统计一个字符串有几个指定的子串：strings.Count("ceheese", "e")

```go
fmt.Printfln(strings.Count("ceheese", "e"))	//4
```

(10)不区分大小写的字符串比较：fmt.Println(strings.EqualFold("abc", "Abc")) 

```go
fmt.Println(strings.EqualFold("abc", "Abc")) // true
```

(11)返回子串在字符串第一次出现的下标值，如果没有返回-1

```go
strings.Index("NLT_abc", "abc") //4
```

(12)返回子串在字符串最后一次出现的 index，如没有返回-1

```go
strings.LastIndex("go golang", "go")
```

(13)将指定的子串替换成另外一个子串: n可以指定你希望替换几个，如果n=-1表示全部替换

```go
strings.Replace("go go hello", "go", "go 语言", n)
```

(14)按照指定的某个字符，为分割标识，将一个字符串拆分成字符串数组

```go
strings.Split("hello,wrold,ok", ",")
```

(15)将字符串的字母进行大小写的转换: strings.ToLower("Go")

#### 2.时间和日期相关函数

时间与日期函数需要导入time包，其中的**结构体类型time.Time表示时间**

(1)获取当前时间

```go
now := time.Now()
fmt.Print(now) 	//2024-02-29 20:42:12.1323412
```

(2)获取其他日期信息

```go
now := time.Now()
now.Year() now.Month() now.Day() now.Hour() now.Minute() now.Second()
```

(3)格式化日期时间

```go
now := time.Now()
fmt.Printf(now.Format("2006/01/02 15:04:05"))  //字符串固定
```

(4)时间常量

```go
const (
Nanosecond	Duration = 1 //纳秒
Microsecond	= 1000 * Nanosecond	//微秒
Millisecond	= 1000 * Microsecond //毫秒Second=1000*Millisecond//秒
Minute	= 60 * Second //分钟
Hour	= 60 * Minute //小时
)
```

(5)time 的 Unix 和 UnixNano 的方法

```go
//Unix函数将t表示为Unix时间，即从时间点January1,1970 UTC到时间点t经过的时间
func(t Time) Unix() int64
//UnixNano将t表示为Unix时间，即从时间点January1,1970 UTC到时间点t经过的时间(单位/纳秒)，如果纳秒为单位的unix时间超出了int64能表示的范围，结果是未定义的
func(t Time) UnixNano() int64

fmt.Print(now.Unix(), now.UnixNano())
//1312345344 12312435456465645767
```

#### 3.内置函数

Golang 设计者为了编程方便，提供了一些函数，这些函数可以直接使用，我们称为 Go 的内置函数https://studygolang.com/pkgdoc

```go
//len 用来求元素个数，比如 string、array、slice、map、channel
len(str)

//new 用来分配内存，主要用来分配值类型，比如 int、float32,struct...返回的是指针
p := new(int)

//make make：用来分配内存，只能用来分配引用类型，比如channel、map、slice。
make(type, size)
```

![image-20240301131445288](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240301131445288.png)

### 四、数组与切片

#### 4.1 数组定义和使用

```go
//声明
var arr [10]int
//直接初始化
var arr = [3]int{1,2,3}
//省略var使用:=
arr := [3]int{1,2,3}
//数组长度不确定，在[]中输入...让编译器自行推导, 也可以不写
var arr = [...]int{1,2,3}
//指定下标初始化
var arr = [5]int{1:1, 3:3}

//二维数组
var arr[2][3]int = [2][3]int{{1,2,3},{4,5,6}}
var arr[2][3]int = [...][3]int{{1,2,3},{4,5,6}}
```

#### 4.2 数组的内存布局

- ==数组的地址可以通过数组名来获取 &intArr==
- 数组的第一个元素的地址，就是数组的首地址
- 数组的各个元素的地址间隔是依据数组的类型决定，比如 int64 -> 8 int32->4...
- 二维数组第二行的元素是接着第一行元素的
- **数组也会默认初始化**

#### 4.3 数组的遍历

使用Go语言独有的结构for-range遍历

```go
func main() {
    //一维数组
    arr := [...]int{1,2,3}
    for i, v := range arr {
        fmt.Println(i, v)
    }
    //二维数组
    arr2 := [...][3]int{{1,2,3}, {4,5,6}}
    for i, v := range arr2 {
        for j, v2 := range v {
            fmt.Print(v2)
        }
        fmt.Println()
    }
}
```

#### 4.4 切片slice基本使用

- ==切片是数组的一个引用，因此切片是引用类型==，在进行传递时，遵守引用传递的机制。
- 切片的**使用和数组类似**，遍历切片、访问切片的元素和求切片长度 len(slice)都一样。
- 切片的长度是可以变化的，因此切片是一个**可以动态变化数组**。

(1)切片的创建如下

```go
//方式一：先定义一个数组，再定义一个切片去引用
var arr = [...]int{1,2,3,4,5}
var slice []int = arr[1:3]
fmt.Println("arr=", arr)
fmt.Println("slice=", slice)
fmt.Println("slice的元素个数是", len(slice))	//2
fmt.Println("slice的容量是", cap(slice))	//动态变化
//方式二：通过make来创建切片
var slice []int = make([]int, 2, 4(可不写))  // 创建了一个长度为2，容量为4的切片
//方式三：定义一个切片，直接就指定具体数组
var slice []int = []int{1,2,3}
```

注意：

- var slice = arr[0:end] 可以简写成var slice = arr[:end]
  var slice = arr[start:len(arr)] 可以简写成var slice = arr[start:]
  var slice = arr[0:len(arr)] 可以简写成var slice = arr[:]
- cap 是一个内置函数，用于统计切片的容量，即最大可以存放多少个元素。
- 切片定义完后，还不能使用，因为本身是一个空的，需要让其引用到一个数组，或者 make 一个空间供切片来使用
- 切片的遍历同数组

(2)**切片可以继续切片**

```go
slice := []int{1,2,3}
slice2 = slice[:]
```

(3)**append函数**：可以对切片进行动态追加

```go
slice := []int{1,2,3}
//通过append直接给slice追加元素
slice = append(slice, 4, 5, 6)
//通过append将切片追加给slice
slice = append(slice, slice2, slice3)
```

==在底层slice扩容时，底层会创建一个新的数组，将所有元素拷贝过去，然后slice重新引用到这个新的数组。地址会改变==

(4)**copy拷贝函数**

```go
slice1 := []int{1,2,3,4,5}
slice2 := make([]int, 10)
copy(slice2, slice1)
fmt.Println(slice1)		//1 2 3 4 5
fmt.Println(slice2)		//1 2 3 4 5 0 0 0 0 0
```

**注意：将长度大的slice拷贝给长度小的slice也不会出错**

#### 4.5 切片在内存中的形式

slice 的确是一个引用类型

slice 从底层来说，其实就是一个数据结构(struct 结构体)

```go
type slice struct {
    ptr *[2]int	//引用数组的地址
    len int  	//长度
    cap int		//容量
}
```

#### 4.6 slice与string

string 底层是一个 byte 数组，因此 string 也可以进行切片处理

```go
str := "qweq"
slice := str[:]
```

==因为string是不可变的，也就说不能通过切片方式来修改字符串==

如果需要修改字符串，方法如下

```go
//先将string->[]byte字节数组，修改完在转换为string
str := "asdf"
arr1 := []byte(str)
arr1[0] = 's'
str = string(arr1)
//先将string->[]rune字符数组，修改完在转换为string
//[]rune按字符处理，可以处理中文
arr1 := []rune(str)
arr1[0] = '你'
str = string(arr1)
```

### 五、map

#### 5.1 map基本语法

##### 5.1.1 map定义

```go
var a map[string]string
```

##### 5.1.2 map初始化方式

```go
//方式一：直接初始化
var a map[string]int = map[string]int{
    "sa":   1,
    "sdf":  2,
    "sdfg": 3,
}
//方式二：make
var a map[string]int = make(map[string]int, 10)
var a map[string]int = make(map[string]int)
```

注意：

- map 的 key 是不能重复，如果重复了，则以最后这个 key-value 为准
- map 的 value 是可以相同的.
- map 的 key-value 是无序
- ==make函数的size可以省略，因为返回的是一个映射==

##### 5.1.3 map的增删改查

- map增加和更新：
  map["key"] = value //**如果key还没有，就是增加，如果key存在就是修改**。
- map删除：
  **delete(map，"key")** ，delete是一个内置函数，如果key存在，就删除该key-value,**如果 key不存在就不操作，但是也不会报错**
- ==查找：
  val, ok := a["sa"]
  val是返回的值，没有就是默认值，ok是是否存在true/flase==

##### 5.1.4 map的遍历

```go
var a map[string]int = map[string]int{
    "sa":   1,
    "sdf":  2,
    "sdfg": 3,
}
for k, v := range a {
    
}
```

#### 5.2 map排序输出方法

先取出map所有key放入切片中，再将切片排序，然后按切片的顺序输出

```go
map1 := map[int]int{
    2: 1, 3:4, 1: 6, 4: 3,
}
var keys []int
for i, _ := range map1 {
    keys = append(keys, i)
}
//排序
sort.Ints(keys)
//输出
for _, i := range keys {
    fmt.Println(map1[i])
}
```

#### 5.3 注意细节

- **map是引用数据类型**,==写操作前必须指向一个地址，make或者初始化==
- map 的容量达到后，再想 map 增加元素，会自动扩容，并不会发生 panic，也就是说 map 能动态的增长 键值对(key-value)
- map 的 **value 也经常使用 struct 类型**，更适合管理复杂的数据(比前面 value 是一个 map 更好)
- ==map，包括所有引用类型使用前必须初始化==

### 六、面向对象编程

Golang 也支持面向对象编程(OOP)，但是和传统的面向对象编程有区别，并不是纯粹的面向对象语言。所以我们说 Golang 支持面向对象编程特性是比较准确的。

Golang 没有类(class)，Go 语言的结构体(struct)和其它编程语言的类(class)有同等的地位，你可以理解 Golang 是基于 struct 来实现 OOP 特性的。

Golang 面向对象编程非常简洁，去掉了传统 OOP 语言的继承、方法重载、构造函数和析构函数、隐藏的 this 指针等等

Golang 仍然有面向对象编程的**继承，封装和多态**的特性，只是实现的方式和其它 OOP 语言不一样，比如继承 ：Golang 没有 extends 关键字，继承是通过匿名字段来实现。

Golang 面向对象(OOP)很优雅，OOP 本身就是语言类型系统(type system)的一部分，通过接口(interface)关联，耦合性低，也非常灵活。后面同学们会充分体会到这个特点。也就是说在Golang 中面向接口编程是非常重要的特性。

#### 6.1 结构体

##### 6.1.1 声明

==变量不用var, 函数不用func==

```go
type cat struct {
    name string
    age int
    color string
}
```

##### 6.1.2 初始化

```go
//方式一
var person Person
//方式二
var person Person = Person{}
//该方式需要全部初始化
var person Person = Person{"afd", 13}
//该方式可以只写部分参数
var person Person = Person{name:"afd", age:13}	
//方式三
var p *Person = new(Person)
//方式四
var p *Person = &Person{}
```

##### 6.1.3 注意细节

- ==结构体是值类型==
- 结构体的所有字段在**内存中是连续**的
- **结构体是用户单独定义的类型，和其它类型可以转换，但进行转换时需要有完全相同的字段(名字、个数和类型)**
- 结构体进行 type 重新定义(相当于取别名)，Golang 认为是新的数据类型，但是相互间可以强转
- struct 的每个字段上，可以写上一个 **tag**, 该 tag 可以通过反射机制获取，常见的使用场景就是序列化和反序列化。
- `如果在结构体内部还要引用自身，那么只能使用指针。`

#### 6.2 方法

Golang中的方法是**作用在指定的数据类型上的**(即：和指定的数据类型绑定)，因此**自定义类型， 都可以有方法**，而不仅仅是 **struct**。

##### 6.2.1 格式

```go
func (p Person) f1() int {
    return 1+2
}
func (p *Person) f1() int {
    return 1+2
}
```

多了一个与特定数据类型的参数，其实就相当于this

##### 6.2.2 注意细节

- 自定义类型，都可以有方法，而不仅仅是struct，比如int,float32等都可以有方法
- **方法不能直接访问，只能有对应类型的对象访问**
- ==方法没有默认的this指针，必须通过给定的对象访问==
- ==如果一个类型实现了 String()这个方法，那么 fmt.Println 默认会调用这个变量的 String()进行输出==
- ==p是一个结构体指针，p.name等价于(*p).name==
- ==若接收者为值类型，那么无论用值还是指针调用该方法，对象的副本传递给了值接收者，方法操作的都是对象的副本。
  若接收者为指针类型，那么无论用值还是指针调用该方法，对象的地址传递给了指针接受者，方法操作的都是对象的指针==

#### 6.3 工厂模式

Golang 的结构体没有构造函数，通常可以使用工厂模式来解决这个问题。使用工厂模式实现跨包创建结构体实例(变量)的案例

```go
package model

type student stuct {
    Name string
    Score float64
}
//不能直接获取就定义一个函数返回结构体对象
func NewStudent(n string, s float64) *student {
    return &student {
        Name:n
        Score:s
    }
}
```

```go
package main
import "fmt"

func main() {
    var stu = model.NewStudent("tom~", 88.8)
}
```

如果 model 包的 **结构体变量首字母大写，引入后，直接使用**,没有问题,如果 model 包的 结构体变量首字母小写，引入后，不能直接使用, 可以**工厂模式解决**

==类似构造函数，如果结构体中的变量也无法直接访问，则可以为结构体定义一个get方法==

#### 6.4 封装

封装的优点：

- 隐藏实现细节
- 提可以对**数据进行验证**，保证安全合理(Age)

如何封装：

- 通过**方法，包**实现
- 对结构体中的属性进行封装

##### 6.4.1 实现步骤

1. 将结构体、字段(属性)的首字母小写(不能导出了，其它包不能使用，类似 private)

2)  给结构体所在包提供一个工厂模式的函数，首字母大写。类似一个构造函数

3. 提供一个首字母大写的Set方法(类似其它语言的public)，用于对属性判断并赋值

4. 提供一个首字母大写的Get方法(类似其它语言的 public)，用于获取属性的值

**特别说明**：在 Golang开发中并没有特别强调封装，这点并不像 Java. 所以提醒学过java 的朋友，不用总是用java的语法特性来看待Golang,Golang本身对面向对象的特性做了简化的.

```go
package entity

type person struct {
	name string
	age  int
	sal  float64
}

func NewPerson(s string) *person {
	return &person{
		name: s,
	}
}

func (p *person) SetAge(age int) {
	p.age = age
}
func (p *person) GetAge() int {
	return p.age
}
```

```go
package test

import (
	"awesomeProject/entity"
	"fmt"
)

func main() {
	p := entity.NewPerson("zs")
	p.SetAge(10)
	fmt.Println(p.GetAge())
}
```

#### 6.5 组合

##### 6.5.1 介绍

**组合可以是以下几种情况：**

- 接口组合接口
- 结构体组合结构体
- 结构体组合结构体指针
- 结构体组合接口

**当 A 组合了 B 之后：**

- 可以直接在 A 上调用 B 的方法。
- B 实现的所有接口，都认为 A 已经实现了。
- A 组合 B 之后，在初始化 A 的时候将 B 看做普通字段来初始化。
- `组合不是继承，没有多态。`

```go
type Goods struct { 
    Name string 
    Price int
}
type Book struct {
	Goods	//这里就是嵌套匿名结构体 Goods Writer string
    num int
}
```

##### 6.5.2 深入讨论

(1)结构体可以**使用嵌套匿名结构体所有的字段和方法**

```go
var b Book
b.Goods.Name = "adf"
b.Goods.Price = 12.4
```

(2)匿名结构体字段访问可以简化

```go
b.Goods.Name -> b.Name
b.Goods.Price -> b.Price
```

(3)**当我们直接通过** **b** **访问字段或方法时，其执行流程如下**比如b.Name

- 编译器会先看 b 对应的类型有没有Name, 如果有，则直接调用B类型的 Name 字段
- 如果没有就去看 B 中嵌入的匿名结构体 A 有没有声明 Name 字段，如果有就调用,如果没有继续查找..如果都找不到就报错.

(4)当**结构体**和**匿名结构体**有相同的字段或者方法时，**编译器采用就近访问原则访问**

(5)嵌套匿名结构体后，也可以在创建结构体时，直接指定各个**匿名结构体字段的**值

```go
b := Book{Goods{"adf", "13.3"}, 20}
```

##### 6.5.3 多重继承

如**一个** **struct** **嵌套了多个匿名结构体**，那么该结构体可以访问多个嵌套的匿名结构体的字段和方法，**从而实现了多重继承**。

- 如嵌入的匿名结构体有相同的字段名或者方法名，则在访问时，需要通过匿名结构体类型名来区分
- 为了保证代码的简洁性，建议大家尽量不使用多重继承

#### 6.6 接口和多态

`多态特征是通过接口实现的`。可以按照统一的接口来调用不同的实现。这时接口变量就呈现不同的形态。

##### 6.6.1 基本语法

里面只能有方法，方法也不需要func关键字和方法体

```go
type 接口名 interface {
    方法名(参数列表)返回值列表 
	方法名(参数列表)返回值列表
}
```



```go
type usb interface {
    start()
    stop()
}
//结构体phone
type phone struct {
    
}
func (p phone) start() {}
func (p phone) stop() {}
//结构体phone
type camera struct {
    
}
func (c camera) start() {}
func (c camera) stop() {}
//working函数
func working(u usb) {
    u.start()
    u.stop()
}

func main() {
    phone := phone{}
    camera := camera{}
    working(phone)
    working(camera)
}
```

##### 6.6.2 注意细节

- ==接口是引用类型==

- 接口本身**不能创建实例**,但是**可以指向一个实现了该接口的自定义类型的变量**(实例)

```go
var p phone
var u usb = p
```

- **接口只能有方法，所有方法都没有方法体和func关键字，方法不需要实现**
- 在Golang中，一个自定义类型需要**将某个接口的所有方法都实现**，我们说这个自定义类型实现了该接口。
- 一个自定义类型只有实现了某个接口，才能将该自定义类型的实例(变量)赋给接口类型
- ==只要是自定义数据类型，就可以实现接口，不仅仅是结构体类型。==
- 一个自定义类型可以实现多个接口
- **一个接口(比如 A 接口)可以继承多个别的接口(比如 B,C 接口)，这时如果要实现 A 接口，也必须将 B,C 接口的方法也全部实现。**
- **空接口interface{}没有任何方法，所以所有类型都实现了空接口, 即我们可以把任何一个变量 赋给空接口，可以类似java的Object**

##### 6.6.3 接口实践

对结构体切片的排序

```go
type Person struct {
	name string
	Age  int
}
//重命名切片，实现interface{}接口函数
type PersonSlice []Person

//实现三个函数
func (p PersonSlice) Len() int {
	return len(p)
}

func (p PersonSlice) Less(i, j int) bool {
	return p[i].Age < p[j].Age
}

func (p PersonSlice) Swap(i, j int) {
	temp := p[i]
	p[i] = p[j]
	p[j] = temp
}

func main() {
	p := PersonSlice{{"sd", 5}, {"d", 2}, {"df", 3}, {"sf", 1}}
	sort.Sort(p)
	for _, person := range p {
		fmt.Println(person.name, person.Age)
	}
}
```

#### 6.7 类型断言

类型断言，由于接口是一般类型，不知道具体类型，如果要转成具体类型，就需要使用类型断言， 

```go
var x interface{}
var a int
a = x.(int)
```

如果接口变量指向某已实现类型的变量，则可以转换，否则报 panic

==在进行断言时，带上检测机制，如果成功就 ok,否则也不要报 panic==

```go
var b float32 = 1.1
var x interface{} = b
//不会报panic
a, ok := x.(float64);
```

### 七、文件操作

<img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240307135118013.png" alt="image-20240307135118013" style="zoom: 80%;" />

**os.File 封装所有文件相关操作，File 是一个结构体**

#### 7.1 打开和关闭文件

```go
//打开文件
file, err := os.Open("D:/1.txt")
if err != nil {
    fmt.Println("open file err:", err)
}
//关闭文件
err = file.close()
if err != nil {
    fmt.Println("close err:", err)
}
```

#### 7.2 读文件

##### 7.2.1 带缓冲区的方式

```go
import (
"fmt"
"os" "bufio" "io"
)

//file是*File类型
file，err := os.Open("d:/test.txt") 
if err != nil {
    fmt.Println("open file err:", err)
}
//当函数退出是要及时关闭文件
defer file.Close();
//创建 *Reader
reader := bufio.NewReader(file)
for {
    //读到一个换行就结束
    str, err := reader.ReadString('\n')
    if err == io.EOF {
        break
    }
    fmt.Print(str)
}
```

reader带缓冲区，缓冲区默认大小为4096

##### 7.2.2 使用 ioutil 一次将整个文件读入到内存中

```go
import (
"fmt"
"io/ioutil"
)
//使用ioutil.ReadFile一次性将文件读取到位
content, err := ioutile.Readfile("d:/test.txt")
if err != nil {
    fmt.Println("read file err", err)	
}
fmt.Print(string(content))
```

- 文件的open和close都封装到了Readfile内部
- content是一个[]byte切片

#### 7.3 写文件

##### 7.3.1 普通方式

```go
file, err := os.OpenFile(file, os.O_WRONLY|os.O_CREATE, 0666)
if err != nil {
    fmt.Println("open file err", err)
}
defer file.close()
//使用*Writer写文件
writer := bufio.NewWriter(file)
writer.writeString("sdf")

writer.Flush()
```

OpenFile函数参数：**文件位置， 打开模式，权限控制**

==writerString会先写到缓存中，所以需要使用Flush方法将数据真正写到文件中==

##### 7.3.2 拷贝文件

```go

file1, err1 := os.Open("D:\\E\\学习\\Go\\test.txt")
file2, err2 := os.OpenFile("D:\\E\\学习\\Go\\test2.txt", os.O_WRONLY|os.O_APPEND, 111)
if err1 != nil {
    fmt.Println("err1")
}
if err2 != nil {
    fmt.Println("err2")
}

defer file1.Close()
defer file2.Close()

reader := bufio.NewReader(file1)
writer := bufio.NewWriter(file2)
io.Copy(writer, reader)
```

也可以通过io.Copy(writer, reader)实现

#### 7.4 判断文件是否存在

![image-20240307201725324](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240307201725324.png)

#### 7.5 命令行参数

##### 7.5.1 普通方法获取

**os.Args** 是一个 string 的切片，用来存储所有的命令行参数，遍历即可

##### 7.5.2 flag 包用来解析命令行参数

```go
//定义几个变量，用于接收命令行的参数值
var user string
var pwd string
var host string
var port int

//&user 就是接收用户命令行中输入的 -u 后面的参数值
//"u", 就是-u指定参数
//"", 默认参数
//"用户名，默认为空"，说明
flag.StringVar(&user, "u", "", "用户名,默认为空")
flag.StringVar(&pwd, "pwd", "", "密码，默认为空")
flag.StringVar(&hose, "h", "localhost", "主机名")
flag.StringVar(&port, "port", 3306, "端口号，默认为3306")

//非常重要的一个操作，必须调用该方法
flag.Parse()
//输出结果
fmt.Prinln(user, pwd, host, port)
```

#### 7.6 json基本介绍

JSON是一种轻量级的**数据交换格式**。易于人阅读和编写。同时也易于及其解析和生成。JSON是在2001年开始推广使用的数据格式，目前已经成为==主流的数据格式==

JSON易于机器解析和生成，通常程序在网络传输时会将数据(结构体、map、切片等)==序列化==成json字符串，到接收方得到json字符串后，再==反序列化==恢复为原来的数据。

##### 7.6.1 json格式

在js语言中，一切都是对象。因此，任何的数据类型都可以通过json来表示，如字符串、数字、对象、数组、map、结构体等

json保存数据的方式是==键值对==，键名用""包裹，冒号后接值

```js
{"key1":val1, "key2":[val2, val3]}, 
```

可以通过网站https://www.json.cn/查询json语句格式是否正确

##### 7.6.2 json的序列化

使用encoding/json包中的Marshal函数

```go
//结构体序列化
type Person struct{
    Name string
    Age int
}

func main(){
    p := {"dsf", 234}
    //返回[]byte
    data, err := json.Marshal(&p) 
}
```

注意：

- Marshal函数接收指针类型
- 为了在其他包中使用结构体的属性，结构体的属性必须大写
- 对于结构体的序列化，如果我们希望序列化后的key 的名字，又我们自己重新制定，那么可以给struct指定一个 tag 标签. 

```go
type Person struct{
    Name string `json:"person_name"`
    Age int		`json:"person_age"`
}
```

##### 7.6.3 json的反序列化

json 反序列化是指，将 json 字符串反序列化成对应的数据类型(比如结构体、map、切片)的操作

通过encoding/json中的Unmashal函数实现

```go
//反序列化结构体

//说明 str 在项目开发中，是通过网络传输获取到..  或者是读取文件获取到
str := "{\"Name\":\"牛魔王\",\"Age\":500,\"Birthday\":\"2011-11-11\",\"Sal\":8000,\"Skill\":\"牛魔拳\"}"
err := json.Unmarshal([]byte(str), &monster)
if err != nil {
    fmt.Println(err)
}
```

- 在反序列化一个json 字符串时，要确保**反序列化后的数据类型**和原来**序列化前的数据类型**一致。
- 如果 json 字符串是通过程序获取到的，则不需要再对 “ 转义处理。

### 八、单元测试

Go 语言中自带有一个轻量级的测试框架 testing 和自带的 go test 命令来实现单元测试和性能测试，testing 框架和其他语言中的测试框架类似，可以基于这个框架写针对相应函数的测试用例，也可以基于该框架写相应的压力测试用例。通过单元测试，可以解决如下问题: 

- #### 确保每个函数是可运行，并且运行结果是正确的

- 确保写出来的代码**性能是好**的，

- 单元测试能及时的发现程序设计或实现的**逻辑错误**，使问题及早暴露，便于问题的定位解决， 而**性能测试**的重点在于发现程序设计上的一些问题，让程序能够在高并发的情况下还能保持稳定

```go

```

\1) 测试用例文件名必须以 _test.go 结尾。 比如 cal_test.go , cal 不是固定的。

\2) 测试用例函数必须以 Test 开头，一般来说就是 Test+被测试的函数名，比如 TestAddUpper

\3) TestAddUpper(t *tesing.T)  的形参类型必须是 *testing.T 【看一下手册】

\4) 一个测试用例文件中，可以有多个测试用例函数，比如 TestAddUpper、TestSub

\5) 运行测试用例指令
cmd>go test   [如果运行正确，无日志，错误时，会输出日志]
cmd>go test -v   [运行正确或是错误，都输出日志]

\6) 当出现错误时，可以使用 t.Fatalf 来格式化输出错误信息，并退出程序

\7) t.Logf 方法可以输出相应的日志

\8) 测试用例函数，并没有放在 main 函数中，也执行了，这就是测试用例的方便之处[原理图].

\9) PASS 表示测试用例运行成功，FAIL 表示测试用例运行失败

\10)  测试单个文件，一定要带上被测试的原文件

go test -v cal_test.go cal.go

\11) 测试单个方法

go test -v -test.run  TestAddUpper

### 九、goroutine和channel

Go主线程(有程序员直接称为线程/也可以理解成进程): 一个 Go 线程上，可以起多个goroutine协程，你可以这样理解，协程是轻量级的线程[编译器做优化]。

Go协程的特点

- 有独立的栈空间
- 共享程序堆空间
- 调度由用户控制
- 协程是轻量级的线程

#### 9.1 goroutine快速入门

```go
func test() {
	for i := 0; i < 10; i++ {
		fmt.Println("hello, Go")
		time.Sleep(time.Second)
	}
}

func main() {
	go test()
	for i := 0; i < 10; i++ {
		fmt.Println("sdfsdf")
		time.Sleep(time.Second)
	}
}
```

- 主线程是一个物理线程，直接作用在 cpu 上的。是重量级的，非常耗费 cpu 资源。
- 协程从主线程开启的，是轻量级的线程，是逻辑态。对资源消耗相对小。
- Golang 的协程机制是重要的特点，可以轻松的**开启上万个协程**。其它编程语言的并发机制是一般基于线程的，开启过多的线程，资源耗费大，这里就突显 Golang 在并发上的优势了
- 主线程结束后其所有协程也会结束

#### 9.2 goroutine加锁

```go
// 声明一个全局的互斥锁
var lock sync.Mutex
var a int = 0

func test() {
	lock.Lock()
	a++
	lock.Unlock()
}
func main() {
	for i := 0; i < 10; i++ {
		go test()
	}
	time.Sleep(time.Second*10)
}
```

#### 9.4 channel

前面使用全局变量加锁同步来解决 goroutine 的通讯，但不完美，主线程在等待所有 goroutine 全部完成的时间很难确定，我们这里设置 10 秒，仅仅是估算。如果主线程休眠时间长了，会加长等待时间，如果等待时间短了，可能还有 goroutine 处于工作状态，这时也会随主线程的退出而销毁通过全局变量加锁同步来实现通讯，也并不利用多个协程对全局变量的读写操作。

上面种种分析都在呼唤一个新的通讯机制-channel

##### 9.4.1 定义/声明和使用

**channel本质是队列。线程安全，多协程访问时，不需要加锁。channel有类型，对应类型的channel存放对应类型的数据**

```go
var 变量名 chan 数据类型
var intChan chan int
//使用前必须make
intChan = make(chan int, 3)
intChan<-10
intChan<-11
num1 := <- intChan
fmt.Println(num1)
```

注意

- channel是引用数据类型，==channel使用前必须make==
- 在没有使用协程的情况下，如果 channel 数据取完了，再取，就会报 dead lock

##### 9.4.2 channel的遍历和关闭

==channel关闭后可读不可写, 遍历时一般用for range前遍历前要关闭channel，如果写完后不关闭，去读会死锁==

```go
var intChan chan int
intChan = make(chan int, 100)
for i := 0; i < 100; i++ {
    intChan <- i
}
//只能用forrange遍历, 遍历前如果不关闭channel,则会报错
close(intChan)
for v := range intChan {
    fmt.Println(v)
}
```

##### 9.4.3 案例

writeData协程，向管道写50个整数

readData协程，向管道读数据

```go
func writeData(intChan chan int) {
	for i := 1; i <= 50; i++ {
		//放入数据
		intChan <- i
		fmt.Println("writeData ", i)
	}
	close(intChan) //关闭
}

func readData(intChan chan int, exitChan chan bool) {
	for {
		time.Sleep(time.Second)
		v, ok := <-intChan
		if !ok {
			break
		}
		//time.Sleep(time.Second)
		fmt.Printf("readData 读到数据=%v\n", v)
	}
	//readData 读取完数据后，即任务完成
	exitChan <- true
	close(exitChan)
}

func main() {
	intChan := make(chan int, 50)
	exitChan := make(chan bool, 1)
	go writeData(intChan)
	go readData(intChan, exitChan)

	for {
		
	}
}
```

- ==当channel已满时继续写会阻塞，当channel为空时继续读也会阻塞，所以容易死锁。chan关闭后再读不会阻塞==
- ==num, ok := <-inChan, 当inChan关闭后且intChan没有数据读后，ok为false，intChan没关闭读会阻塞==

##### 9.4.4 注意细节

1) channel 可以声明为只读，或者只写性质

```go
//只写
var chan1 chan<- int
//只读
var chan2 <-chan int
```

2. 使用 select 可以解决从管道取数据的阻塞问题

```go
intChan := make(chan int, 1)
//如果intChan一直没有关闭，不会一直阻塞而 deadlock,会自动到下一个case匹配
select {
    case v := <-intChan:
    fmt.Println(v)
    default:
}
```

3. goroutine 中使用 recover，解决协程中出现 panic，导致程序崩溃问题

### 十、反射

\1)   反射可以在运行时**动态获取变量的各种信息**, 比如变量的类型(type)，类别(kind)

\2)   如果是结构体变量，还可以获取到结构体本身的信息(包括结构体的**字段**、**方法**)

\3)   通过反射，可以修改变量的值，可以调用关联的方法。

\4)   使用反射，需要 import (“**reflect**”)

#### 10.1 快速入门

```go
func reflectTest(b interface{}) {
    //通过反射获取传入的变量的type,kind
    //先获取reflect.Type
    rT := reflect.TypeOf(b)
    //获取reflect.Value
    rV := reflect.ValueOf(b)
    //rv转换为interface{}
    iv := rV.Interface()
    //iv通过类型断言转换
    stu, ok := iv.(Student)
}

//获取到该结构体有几个字段
num := val.NumField()
//遍历结构体每个字段
for i := 0; i < num; i++ {
    fmt.Printf("Field %d:  值为=%v\n", i, val.Field(i))
    //获取到 struct 标签, 注意需要通过 reflect.Type 来获取 tag 标签的值
    tagVal := typ.Field(i).Tag.Get("json")
    //如果该字段于 tag 标签就显示，否则就不显示
    if tagVal != "" {
    	fmt.Printf("Field %d: tag 为=%v\n", i, tagVal)
    }
}
```

#### 10.2 注意细节

- reflect.Value.Kind，获取变量的类别，返回的是一个常量
- Type 是类型, Kind 是类别， Type 和 Kind 可能是相同的，也可能是不同的
  var num int = 10  num 的 Type 是 int , Kind 也是 int
  var stu Student  stu 的 Type 是 pkg1.Student , Kind 是 struct
- 通过反射可以让变量在interface{}和reflect.Value之间转换
- 通过反射的来修改变量, 注意当使用 SetXxx 方法来设置需要通过对应的指针类型来完成, 这样才能改变传入的变量的值, 同时需要使用到 reflect.Value.Elem()方

```go
var num int = 100
rv := reflect.ValueOf(&num)	//需要传入内存地址
rv.Elem().SetInt(200)
fmt.Println(num)
```

#### 10.3 案例

 使用**反射来遍历结构体的字段**，**调用结构体的方法**，并**获取结构体标签**的值

```go
//定义了一个 Monster 结构体
type Monster struct {
	Name		string `json:"name"` 
    Age	int `json:"monster_age"` 
    Score float32 `json:"成绩"`
	Sex	string
}

//方法，返回两个数的和
func (s Monster) GetSum(n1, n2 int) int { 
    return n1 + n2
}
//方法， 接收四个值，给 s 赋值
func (s Monster) Set(name string, age int, score float32, sex string) { 
    s.Name = name
	s.Age = age 
    s.Score = score 
    s.Sex = sex
}

//方法，显示 s 的值
func (s Monster) Print() { 
    fmt.Println("---start~	")
	fmt.Println(s) 
    fmt.Println("---end~	")
}
func TestStruct(a interface{}) {
    //获取 reflect.Type 类型
    typ := reflect.TypeOf(a)
    //获取 reflect.Value 类型
    val := reflect.ValueOf(a)
    //获取到 a 对应的类别
    kd := val.Kind()
    //如果传入的不是 struct，就退出if kd !=	
    reflect.Struct {
		fmt.Println("expect struct") return
	}
    //获取到该结构体有几个字段
    num := val.NumField()
    fmt.Printf("struct has %d fields\n", num) //4
    //变量结构体的所有字段
    for i := 0; i < num; i++ {
    	fmt.Printf("Field %d:  值为=%v\n", i, val.Field(i))
        //获取到struct标签, 注意需要通过 reflect.Type 来获取 tag 标签的值
        tagVal := typ.Field(i).Tag.Get("json")
        //如果该字段于 tag 标签就显示，否则就不显示
        if tagVal != "" {
            fmt.Printf("Field %d: tag 为=%v\n", i, tagVal)
        }
    }
    //获取到该结构体有多少个方法
    numOfMethod := val.NumMethod()
	fmt.Printf("struct has %d methods\n", numOfMethod)
	//var params []reflect.Value
	//方法的排序默认是按照 函数名的排序（ASCII 码）
	val.Method(1).Call(nil) //获取到第二个方法。调用它

	//调用结构体的第 1 个方法 Method(0)
	var params []reflect.Value	//声明了 []reflect.Value 
    params = append(params, reflect.ValueOf(10)) 
    params = append(params, reflect.ValueOf(40))
    //传入的参数是 []reflect.Value, 返回[]reflect.Value 
	res := val.Method(0).Call(params) 
    //返回结果, 返回的结果是 []reflect.Value*/
    fmt.Println("res=", res[0].Int()) 
}
func main() {
    //创建了一个 Monster 实例
    var a Monster = Monster{ Name: "黄鼠狼精", 
                            Age: 400,
                            Score: 30.8,}
    //将 Monster 实例传递给 TestStruct 函数
    TestStruct(a)
}
```

### 十一、TCP编程

使用netstat -an查看本机有哪些端口在监听

使用netstat -anb查看监听端口的 pid,在结合任务管理器关闭不安全的端口

使用telnet测试服务器连接:telnet 127.0.0.1 8888 

取消一个端口的监听：先查找这个端口的 pid>netstat -anb | findstr 8889
再taskkill /F /PID 23212

#### 11.1 快速入门

server.go

```go
package main

import (
	"fmt"
	"net"
)
//协程与客户端通信
func process(conn net.Conn) {
	defer conn.Close()
	//循环接收
	for {
		buf := make([]byte, 1024)
		//如果客户端没有发送就会一直阻塞
        n, err := conn.Read(buf[0:1024]})
		if err != nil {
			fmt.Println("服务器端read出错：", err)
			return
		} else {
			//在终端显示
			fmt.Println(string(buf[:n]))
		}
	}
}

func main() {
	fmt.Println("服务期开始监听...")
	//使用tcp协议，在本地监听8888端口
	listen, err := net.Listen("tcp", "0.0.0.0:8888")
	if err != nil {
		fmt.Println("listen err:", err)
	}
	//延时关闭
	defer listen.Close()
	//循环等待客户端来连接
	for {
		//等待客户端连接
		fmt.Println("等待连接...")
		conn, err := listen.Accept()
		if err != nil {
			fmt.Println("Accept err:", err)
		} else {
			fmt.Println("连接成功，客户端ip:", conn.RemoteAddr().String())
			go process(conn)
		}
	}
}
```

client.go

```go
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	conn, err := net.Dial("tcp", "127.0.0.1:8888")
	if err != nil {
		fmt.Println("client dial err:", err)
	} else {
		fmt.Println("连接成功:", conn)
	}
	for {
		//从终端读取一行
		reader := bufio.NewReader(os.Stdin)
		line, err := reader.ReadString('\n')
        //去除换行符
		line = strings.Trim(line, "\n")
		if err != nil {
			fmt.Println(err)
		}
    
		n, err := conn.Write([]byte(line))
		if err != nil {
			fmt.Println("write err:", err)
		}
		fmt.Println("已发送", n, "字节")
	}
}
```

#### 11.2 海量用户即时通讯系统

# 进阶

## 网络通信

### go实现TCP

TCP/IP([Transmission](https://so.csdn.net/so/search?q=Transmission&spm=1001.2101.3001.7020) Control Protocol/Internet Protocol) 即传输控制协议/网间协议，是一种面向连接（连接导向）的、可靠的、基于字节流的传输层（Transport layer）通信协议，因为是面向连接的协议，数据像水流一样传输，会存在黏包问题。

一个TCP服务端可以同时连接很多个客户端，多个客户端可以同时像服务端发送消息，go语言中我们也可以使用线程来处理模拟多个客户端发送服务端处理请求

Go语言可以使用net包代码实现TCP服务端代码
步骤：
1 启动监听端口
2 服务端响应返回消息

一个TCP客户端进行TCP通信的流程如下：

1 建立与服务端的链接
2 进行数据收发
3 关闭链接

**tcp的服务端**

```cpp
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

// TCP 服务端
func process(conn net.Conn) {
	// 函数执行完之后关闭连接
	defer conn.Close()
	// 输出主函数传递的conn可以发现属于*TCPConn类型, *TCPConn类型那么就可以调用*TCPConn相关类型的方法, 其中可以调用read()方法读取tcp连接中的数据
	fmt.Printf("服务端: %T\n", conn)
	for {
		var buf [128]byte
		// 将tcp连接读取到的数据读取到byte数组中, 返回读取到的byte的数目
		n, err := conn.Read(buf[:])
		if err != nil {
			// 从客户端读取数据的过程中发生错误
			fmt.Println("read from client failed, err:", err)
			break
		}
		recvStr := string(buf[:n])
		fmt.Println("服务端收到客户端发来的数据：", recvStr)
		// 由于是tcp连接所以双方都可以发送数据, 下面接收服务端发送的数据这样客户端也可以收到对应的数据
		inputReader := bufio.NewReader(os.Stdin)
		s, _ := inputReader.ReadString('\n')
		t := strings.Trim(s, "\r\n")
		// 向当前建立的tcp连接发送数据, 客户端就可以收到服务端发送的数据
		conn.Write([]byte(t))
	}
}

func main() {
	// 监听当前的tcp连接
	listen, err := net.Listen("tcp", "127.0.0.1:20000")
	fmt.Printf("服务端: %T=====\n", listen)
	if err != nil {
		fmt.Println("listen failed, err:", err)
		return
	}
	for {
		conn, err := listen.Accept() // 建立连接
		fmt.Println("当前建立了tcp连接")
		if err != nil {
			fmt.Println("accept failed, err:", err)
			continue
		}
		// 对于每一个建立的tcp连接使用go关键字开启一个goroutine处理
		go process(conn) 
	}
}
```

**客户端**

```cpp
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	// 连接到服务端建立的tcp连接
	conn, err := net.Dial("tcp", "127.0.0.1:20000")
	// 输出当前建Dial函数的返回值类型, 属于*net.TCPConn类型
	fmt.Printf("客户端: %T\n", conn)
	if err != nil {
		// 连接的时候出现错误
		fmt.Println("err :", err)
		return
	}
	// 当函数返回的时候关闭连接
	defer conn.Close() 
	// 获取一个标准输入的*Reader结构体指针类型的变量
	inputReader := bufio.NewReader(os.Stdin)
	for {
		// 调用*Reader结构体指针类型的读取方法
		input, _ := inputReader.ReadString('\n') // 读取用户输入
		// 去除掉\r \n符号
		inputInfo := strings.Trim(input, "\r\n")
		// 判断输入的是否是Q, 如果是Q则退出
		if strings.ToUpper(inputInfo) == "Q" { // 如果输入q就退出
			return
		}
		_, err = conn.Write([]byte(inputInfo)) // 发送数据
		if err != nil {
			return
		}
		buf := [512]byte{}
		// 读取服务端发送的数据
		n, err := conn.Read(buf[:])
		if err != nil {
			fmt.Println("recv failed, err:", err)
			return
		}
		fmt.Println("客户端接收服务端发送的数据: ", string(buf[:n]))
	}
}
```

### Go实现UDP

UDP协议（User Datagram [Protocol](https://so.csdn.net/so/search?q=Protocol&spm=1001.2101.3001.7020)）中文名称是用户数据报协议，是OSI（Open System Interconnection，开放式系统互联）参考模型中一种无连接的传输层协议，不需要建立连接就能直接进行数据发送和接收，是不可靠的、没有时序的通信，但是UDP协议的实时性比较好，通常用于视频直播等场景。
还是使用Go语言的net包实现的UDP服务端代码

**服务端**

```cpp
package main

import (
	"fmt"
	"net"
)

//服务端接收信息
func main() {
	udp, err := net.ListenUDP("udp", &net.UDPAddr{
		IP: net.IPv4(0, 0, 0, 0),
		Port: 9999,
	})
	if err!=nil {
		fmt.Println("网络错误")
		return

	}
	defer udp.Close()
	for  {
		var b[1024] byte
		n, addr, err := udp.ReadFromUDP(b[:])
		if err!=nil {
			fmt.Println("读取数据错误 ： ", err)
			continue
		}
		fmt.Printf(" %s 客户端发送的信息 ： %s \n ",addr, string(b[:n]))
		udp.WriteTo([]byte("收到了"),addr)
	}
}
```

**客户端**

```cpp
package main

import (
	"fmt"
	"net"
)

func main() {
	dial, err := net.Dial("udp", "0.0.0.0:9999")
	if err!=nil {
		fmt.Println("网络错误")
		return

	}
	defer dial.Close()
	_, err = dial.Write([]byte("hello...."))
	if err!=nil {
		fmt.Println("发送数据失败")
		return
	}
	//接收数据
	var b[1024]byte
	n, err := dial.Read(b[:])
	if err!=nil {
		fmt.Println("接收数据失败")
		return
	}
	fmt.Println("接收的消息：", string(b[:n]))
}
```

### net/http

#### Web 应用的工作原理

![image-20240316121101383](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240316121101383.png)

#### 基本案例

下面，就让我们使用 Go 语言创建一个简单的 Web 应用。

1） 在 GOPATH 下的 src 目录下

```go
package main
import (
"fmt"
"net/http"
)
// 创建处理器函数
func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello world!", r.URL.Path)
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}
```

2） 在终端执行以下命令（使用 Vscode 开发工具时）：

​	a) 方式一（建议使用）：在 webapp 目录中右键→在命令提示符中打开
​		执行 go build main.go 命令；然后在当前目录中就会生成一个 main.exe 的
​		二进制可执行文件；最后再执行 ./main.exe 就可以启动服务器
​	b) 方式二：在 webapp 目录中右键→在命令提示符中打开

3） 在浏览器地址栏输入 http://localhost:8080，在浏览器中就会显示 Hello World! 
在浏览器地址栏输入 http://localhost:8080/hello，在浏览器中就会显示 Hello World!

#### Web服务器的创建

##### 简介

Go 提供了一系列用于创建 Web 服务器的标准库。通过 net/http 包调用 ListenAndServe 函数并传入网络地址以及负责处理请求的处理器( handler )作为参数就可以了。

如果网络地址参数为空字符串，那么服务器默认使用 80 端口进行网络连接；如果处理器参数为 nil，那么服务器将使用默认的多路复用器 DefaultServeMux，当然，我们也可以通过调用 NewServeMux 函数创建一个多路复用器。多路复用器接收到用户的请求之后根据请求的 URL 来判断使用哪个处理器来处理请求，找到后就会重定向到对应的处理器来处理请求，

##### 使用默认的多路复用器（DefaultServeMux）

HandleFunc方法和Handle方法区别

使用handle函数接收处理器，只要某个结构体实现了 Handler 接口中的 ServeHTTP 方法
那么它就是一个处理器

```go
type MyHandler struct{}

func (m *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "通过自己创建的处理器处理请求！")
}

func main() {
	myHandler := MyHandler{}
	http.Handle("/myHandler", &myHandler)
    //创建路由
	http.ListenAndServe(":8080", nil)
}
```

通过 Server 结构对服务器进行更详细的配置

```go
type MyHandler struct{}

func (m *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "通过详细配置处理器来处理请求！")
}

func main() {
	myHandler := MyHandler{}
	server := http.Server{
        Addr: ":8080",
        Handler: &myHandler,
        ReadTimeout: 2 * time.Second,
    }
    server.ListenAndServe()
}
```

##### 创建自己的多路复用器

```go
func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "通过自己创建的多路复用器处理请求！")
}

func main() {
	//创建多路复用器
	mux := http.NewServeMux()

	mux.HandleFunc("/", handler)
	//创建路由
	http.ListenAndServe(":8080", mux)
}
```

请求报文
get/hello/index.jsp HTTP/1.1  //没有请求体

post/hello/index.jsp HTTP/1.1 
name:zs
pwd:123

//响应报文
HTTP/1.1 200 OK

#### 处理请求

获取请求行，请求头，请求体，请求参数

```go
// 创建处理器函数
func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "你发送的请求的请求地址是：", r.URL.Path)
	fmt.Fprintln(w, "你发送的请求的请求地址后的查询字符串(请求参数)是：", r.URL.RawQuery)
	fmt.Fprintln(w, "请求头中的所有信息；", r.Header)
	fmt.Fprintln(w, "请求头中Accept-Encoding的信息是", r.Header["Accept_Encoding"])
	fmt.Fprintln(w, "请求头中Accept-Encoding的属性值是", r.Header["Accept_Encoding"])

	//获取请求体内容长度
	len := r.ContentLength
	//创建byte切片
	body := make([]byte, len)
	//将请求体中的内容读到body中
	r.Body.Read(body)
	fmt.Fprintln(w, "请求体中的内容有：", string(body))

	//解析表单，在调用r.Form之前必须执行该操作
	r.ParseForm()
	//获取请求参数
	//如果form表单的action属性的URL地址中也有与form表单参数名相同的请求参数，
	//那么参数值都可以得到，并且form表单中的参数值在URL参数值前面
	fmt.Fprintln(w, "请求参数有：", r.Form)
	fmt.Fprintln(w, "POST请求的form表单中的请求参数有：", r.PostForm)
	//通过直接调用FormValue方法和PostFormValue直接获取请求参数的值
	fmt.Fprintln(w, "请求参数username的值为：", r.FormValue("username"))
	fmt.Fprintln(w, "请求参数username的值为：", r.PostFormValue("username"))
}
```

给客户端响应

```go
func handler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("你的请求我已经收到"))
    
    html := `
    <html>
        <head>
            <title>测试响应内容为网页</title>
            <meta charset="utf-8"/>
        </head>
        <body>
            我是以网页的形式响应过来的！
        </body>
    </html>`
    w.Write([]byte(html))
    
    //设置响应头中内容的类型
    w.Header().Set("Content-Type", "application/json")
    user := User{
        ID: 1,
        Username: "admin",
        Password: "123456",
    }
    //将 user 转换为 json 格式
    json, _ := json.Marshal(user)
    w.Write(json)
    
    //让客户端重定向
    //以下操作必须要在 WriteHeader 之前进行
    w.Header().Set("Location", "https:www.baidu.com")
    //设置响应的状态码
    w.WriteHeader(302)
}
```

### websocket

使用如下包

```bash
go get github.com/gorilla/websocket
```

server

```go
package main

import (
	"fmt"
	"github.com/gorilla/websocket"
	"net/http"
)

var UP = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

// 保存与所有用户的连接
var conns []*websocket.Conn

func handler(w http.ResponseWriter, r *http.Request) {
	//将http服务升级为websocket服务
	conn, err := UP.Upgrade(w, r, nil)
	if err != nil {
		fmt.Println("up.upgrade err:", err)
		return
	}
	conns = append(conns, conn)
	//循环接收客户消息
	for {
		//m表示消息类型（int），p是收到的消息，err判断客户端是否关闭
		m, p, err := conn.ReadMessage()
		if err != nil {
			break
		}
		//向所有客户端发送消息
		for i, _ := range conns {
			conns[i].WriteMessage(websocket.TextMessage, []byte(p))
		}
		fmt.Println(m, string(p))
	}
	defer conn.Close()
	fmt.Println("服务关闭")
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8888", nil)
}

```

client

```go
package main

import (
	"bufio"
	"fmt"
	"github.com/gorilla/websocket"
	"os"
)

func main() {
	dl := websocket.Dialer{}
	conn, _, err := dl.Dial("ws://127.0.0.1:8888", nil)
	if err != nil {
		fmt.Println("dl.dail err:", err)
		return
	}
	//发消息
	go send(conn)
	//读
	for {
		m, p, err := conn.ReadMessage()
		if err != nil {
			break
		}
		fmt.Println(m, string(p))
	}
}

// 从控制台输入，发送消息
func send(conn *websocket.Conn) {
	for {
		reader := bufio.NewReader(os.Stdin)
		line, _, _ := reader.ReadLine()
		conn.WriteMessage(websocket.TextMessage, line)
	}
}

```

#### 跨域问题



## 并发编程

[go并发编程](https://blog.csdn.net/qq_41854911/article/details/121239625)

### goroutine基本使用

使用goroutine非常简单，首先编写一个需要调用的函数，然后使用**go关键字**调用函数即可，这样就创建了一个goroutine并开始执行

```go
func f1() {
    fmt.Println("hello")
}
func main() {
    go f1()
}
```

匿名函数也支持使用`go`关键字创建 goroutine 去执行。

```go
func main() {
    go func() {
        fmt.Println("hello")
    }
    //使用sleep函数等待1s
    time.Sleep(time.Second)
}
```

- Go程序启动时，Go程序就会为main函数创建一个默认的goroutine，其他协程都是从主线程创建的。**主线程结束后其所有协程也会结束**
- Golang的协程栈空间较小(2kB)，可以轻松的开启上万个协程

### channel

使用全局变量加锁同步来解决goroutine的通讯，需要共享内存

channel**不通过共享内存来通信**。其本质是队列，**遵循先进先出的原则**。同时只能有一个goroutine访问通道，不需要加锁。channel有类型，对应类型的channel存放对应类型的数据

#### 定义/声明

```go
//var 变量名 chan 数据类型
var intChan chan int
intChan := make(chan int, 10)
```

`channel 可以声明为只读，或者只写性质`

```go
//只写
var chan1 chan<- int
//只读
var chan2 <-chan int
```

#### 基本使用

```go
//存入
intChan<-10
//取出
num1 := <- intChan
num2, ok := <- intChan
//关闭
close(intChan)
```

**注意**

- channel是引用数据类型，==channel使用前必须make==
- 在没有使用协程的情况下，如果 channel 数据取完了，再取就会报dead lock死锁
- ==channel关闭后可读不可写==
- **当channel已满时继续写会阻塞，当channel为空时继续读也会阻塞，所以容易死锁。chan关闭后再读不会阻塞**
- **==num, ok := <-inChan==, 当inChan关闭后且intChan没有数据读后，ok为false，没关闭且没数据读会阻塞**

#### 有缓存和无缓冲通道

本质就是make初始化指定的大小不同

无缓冲channel，make时不指定大小或者大小为0，在向其中写数据时会一直阻塞知道有接收方向该channel执行读操作

```go
func f1(ch chan int) {
	fmt.Println(<-ch)
}
func main() {
    //或者ch := ma
	ch := make(chan int, 0)
	go f1(ch)
	ch <- 2
	time.Sleep(time.Second)
}
```

#### channel的遍历

channel的遍历**一般用for range**，因为遍历读channel要将元素取出，会改变channel的长度，如果用for i的话会有问题。

```go
var intChan chan int
intChan = make(chan int, 100)
for i := 0; i < 100; i++ {
    intChan <- i
}
close(intChan)
for v := range intChan {
    fmt.Println(v)
}
```

#### select多路复用

select语句类似switch，它会**随机挑选一个没有阻塞的case语句执行，**

```go
ch1, ch2 := make(chan int, 1), make(chan int, 1)
select {
    case <-ch1:
    fmt.Println(1)
    case <-ch2:
    fmt.Println(2)
    default:
    fmt.Println(3)
}
```

**select的特点**：

- 如果多个case同时满足，select会**随机**选择一个执行。
- 如果都阻塞了，就执行default后的语句
- 对于没有case的空select会一直阻塞，可用于阻塞main函数，防止退出。

### 并发安全和锁/sync包

#### 并发问题

多个协程同时对全局共享数据操作时，会有数据竞争产生错误

```go
var (
	x  int
	wg sync.WaitGroup
)

func add() {
	for i := 0; i < 1000; i++ {
		x++
	}
	wg.Done()
}

func main() {
	wg.Add(3)
	for i := 0; i < 3; i++ {
		go add()
	}
	wg.Wait()
	fmt.Println(x)
}
```

输出的x很有可能小于3000，因为多个协程对全局变量x进行加1时，某个 goroutine 中对全局变量`x`的修改可能会覆盖掉另一个goroutine中的操作

#### 互斥锁Mutex

互斥锁是一种常用的控制共享资源访问的方法，它能够**保证同一时间只有一个 goroutine可以访问共享资源**。Go 语言中使用`sync`包中提供的`Mutex`类型来实现互斥锁。

`sync.Mutex`提供了两个方法供我们使用。只需要对全局变量操作前获取锁，操作完后释放锁即可

```go
//获取锁
func (m *Mutex) Lock()
//释放互斥锁
func (m *Mutex) Unlock()

var m sync.Mutex
m.Lock()
m.Unlock()
```

#### 读写互斥锁RWMutex

互斥锁是完全互斥的，**但读读不互斥，写读互斥，写写互斥**

```go
mutex sync.RWMutex
//加写锁
mutex.Lock
//释放写锁
mutex.Unlock()

//加读锁
mutex.RLock()
//释放读锁
mutex.RUnlock()
```

#### sync.WaitGroup

在main函数中使用time.Sleep等待协程运行完成是不合适的，Go语言中可以使用`sync.WaitGroup`来实现并发任务的同步

```go
//计数器+x
func (wg *WaitGroup) Add(x int)
//计数器-1
func (wg *WaitGroup) Done()
//阻塞当前协程直到计数器变为0
func (wg *WaitGroup) Wait()
```

使用WaitGroup控制main函数结束，达到只有f协程运行完后，main函数才会结束的效果

```go
var wg sync.WaitGroup

func f() {
    fmt.Println("hello")
    wg.Done()
}

func main() {
    wg.Add(1)
    go f()
    wg.Wait()
}
```

#### sync.Once

使用 sync.Once，你可以确保一个函数只执行一次，不管它被调用了多少次或者有多少 goroutines 同时调用它

```go
var (
	x    int
	wg   sync.WaitGroup
	once sync.Once
)

func main() {
	for i := 0; i < 3; i++ {
        wg.Add(1)
		go func() {
            //执行x+1操作，但是这个操作只会执行一次
			once.Do(func() {
				x++
			})
			wg.Done()
		}()
	}
	wg.Wait()
	fmt.Println(x) //1
}
```

#### sync.Map

Go语言中内置的 map 不是并发安全的，sync`包中提供了一个开箱即用的并发安全版map——`sync.Map

```go
var mp = sync.Map{}
//存储
mp.Store(key, value)
//查询key对应的value
mp.Load(key)
//删除key
mp.Delete(key)

//查询并删除key
mp.LoadAndDelete(key)
//查询或存储key对应的value
mp.LoadOrStore(key, value)
```



### 练习题

#### 1

writeData协程，向管道写50个整数

readData协程，向管道读数据

```go
func writeData(intChan chan int) {
	for i := 1; i <= 50; i++ {
		//放入数据
		intChan <- i
		fmt.Println("writeData ", i)
	}
	close(intChan) //关闭
}

func readData(intChan chan int, exitChan chan bool) {
	for {
		time.Sleep(time.Second)
		v, ok := <-intChan
		if !ok {
			break
		}
		//time.Sleep(time.Second)
		fmt.Printf("readData 读到数据=%v\n", v)
	}
	//readData 读取完数据后，即任务完成
	exitChan <- true
	close(exitChan)
}

func main() {
	intChan := make(chan int, 50)
	exitChan := make(chan bool, 1)
	go writeData(intChan)
	go readData(intChan, exitChan)

	for {
		
	}
}
```

#### 2

使用 goroutine 和 channel 实现一个计算int64随机数各位数和的程序，例如生成随机数61345，计算其每个位数上的数字之和为19。

1. 开启一个 goroutine 循环生成int64类型的随机数，发送到`jobChan`
2. 开启24个 goroutine 从`jobChan`中取出随机数计算各位数的和，将结果发送到`resultChan`
3. 主 goroutine 从`resultChan`取出结果并打印到终端输出

```go
func main() {
   jobChan, resultChan := make(chan int64, 1), make(chan int64, 1)
   go func() {
      for {
         n := rand.Int64()
         jobChan <- n
      }
   }()
   for i := 0; i < 24; i++ {
      go func() {
         for {
            n := <-jobChan
            var sum int64
            for n != 0 {
               sum += n % 10
               n /= 10
            }
            resultChan <- sum
         }
      }()
   }
   for {
      fmt.Println(<-resultChan)
   }
}
```

## 泛型

```go
// A
// 泛型接口，T表示变量类型
type A[T any] interface {
   add(val T)
}

// Person 泛型结构体
type Person[T any] struct {
   age T
}

// 泛型方法，其中Number是一个泛型约束
func f1[T Number](data ...T) T {
   var res T
   for _, v := range data {
      res += v
   }
   return res
}
//泛型约束语法
type Number interface {
   int | int32 | float64
}

func main() {
	f1[Number](1,2,3)
	var p Person[int]
}
```



## context

[csdn context解释](https://blog.csdn.net/Guzarish/article/details/119627758?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171401508216800188564455%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171401508216800188564455&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-119627758-null-null.142^v100^pc_search_result_base2&utm_term=go%20context&spm=1018.2226.3001.4187)

### Context接口

`context.Context`是一个接口，该接口定义了四个需要实现的方法。具体签名如下：

```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```

其中：

- Deadline：获取当前 context 的截止时间。
- Done：获取一个只读的 channel，类型为结构体。可用于识别当前 channel 是否已经被关闭，其原因可能是到期，也可能是被取消了。
- Err：获取当前 context 被关闭的原因。
- Value：获取当前 context 对应所存储的上下文信息。

### Background()和TODO()

context包的这两个函数分别返回一个实现了`Context`接口的`background`和`todo`

`background`和`todo`函数本质上都是`emptyCtx`结构体类型，是一个不可取消，没有设置截止时间，没有携带任何值的Context。

### with系列函数

#### WithCancel

`WithCancel`的函数签名如下：

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
```

`WithCancel`返回一个新的基于父context的ctx和一个可以取消内置done通道的函数。当调用返回的cancel函数，**将关闭Done通道**

```go
func work(ctx context.Context) {
   for {
      select {
      case <-ctx.Done():
         return
      default:
      }
   }
}

func main() {
   ctx, cancel := context.WithCancel(context.Background())
   defer cancel()
   go work(ctx)
   //3秒后介绍word协程
   time.Sleep(3 * time.Second)
}
```

#### WithDeadline

`WithDeadline`的函数签名如下：

```go
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
```

创建一个具有截止时间(Deadline)以及cancel函数的新context，当到达截止日期时，自动关闭done通道

```go
var wg sync.WaitGroup

func work(ctx context.Context) {
	for {
		select {
		case <-ctx.Done():
			wg.Done()
			return
		default:
		}
	}
}

func main() {
	wg.Add(1)
	d := time.Now().Add(2 * time.Second)
	ctx, cancel := context.WithDeadline(context.Background(), d)
	// 尽管ctx会过期，但在任何情况下调用它的cancel函数都是很好的实践。
	// 如果不这样做，可能会使上下文及其父类存活的时间超过必要的时间。
	defer cancel()
	go work(ctx)
	wg.Wait()
	fmt.Println("已关闭word")
}
```

#### WithTimeOut

`WithTimeout`的函数签名如下：

```go
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

`WithTimeout`返回`WithDeadline(parent, time.Now().Add(timeout))`。

#### WithValue

`WithValue`函数能够将请求作用域的数据与 Context 对象建立关系。声明如下：

```go
func WithValue(parent Context, key, val interface{}) Context
```

```go
func work(ctx context.Context) {
   for {
      select {
      case <-ctx.Done():
         //可以根据key获取到value
         v := ctx.Value("zs")
         fmt.Println(v)
         wg.Done()
         return
      default:
      }
   }
}

func main() {
   wg.Add(1)
   ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
   ctx = context.WithValue(ctx, "zs", "123")
   defer cancel()
   go work(ctx)
   wg.Wait()
}
```

### 注意事项

- 给一个函数方法传递Context的时候，不要传递nil，如果不知道传递什么，就使用context.TODO()或Background()
- with系列函数传入context参数会基于这个传入的context创建一个新的context，继承了一些属性

## 切片

在Go语言中，切片的底层是一个结构体，该结构体包含三个字段：

- 指向底层数组的指针（ptr）：指向切片所引用的底层数组的指针。
- 切片的长度（len）：表示切片当前包含的元素个数。
- 切片的容量（cap）：表示切片从第一个元素开始到底层数组末尾的元素个数。

```go
type slice struct {
    ptr *elementType   // 指向底层数组的指针
    len int            // 切片的长度
    cap int            // 切片的容量
}
```

当切片的容量不足以容纳更多元素时，Go语言会自动分配一个更大的底层数组，并将切片的指针指向新的底层数组

### 1.切片的创建

- 先创建数组,然后通过截取,来得到该数组的切片
- 使用make函数来创建切片
- 直接初始化，隐式创建匿名数组

```go
arr := [3]int{1,2,3}
//截取
s1 := arr[:]
//make创建
s2 := make([]int, 2)
//隐式截取
s3 := []int{1,2,3}
```



### 2.append操作

```go
s1 := []int{1,2,3}
s2 := []int{6,7,8}
s := append(s1, s2...) //1 2 3 6 7 8
s = append(s, 1) //1 2 3 6 7 8 1

var s3 []int
s3 = append(s3, 1) //1
```

`append()`函数会将元素追加到切片的末尾，并返回一个新的切片。如果原始切片的容量足够大，那么`append()`函数会直接将元素追加到原始切片的末尾。如果原始切片的容量不够大，`append()`函数会创建一个新的切片，并将原始切片的元素和新元素都复制到新的切片中

append()函数可以接收可变参数，使用切片作可变参数要加...将元素打散。

```go
s1 := []int{1, 2}
s2 := s1
s2 = append(s2, 3)
//此时因为s2扩容了，s1与s2的地址不一样了
```

## map

### key的类型

bool、数字、string、指针、channel , 还可以是只包含前面几个类型的接口、结构体、数组。

**注意：**slice、map 和 function 不可以。因为这几个没法用 == 来判断。

### value的类型

和 key 基本一样，但可以是切片，map

### 声明

```go
var m map[string]int
//空map不能直接使用
m["sf"] = 1
//直接初始化，可以直接使用
var m2 map[string]int = map[string]int{}
```

声明是不会分配内存的，初始化需要 make ，分配内存后才能赋值和使用。

## GMP

### 背景

| 单进程时代      | 每个程序就是一个进程，直到一个程序运行完，才能进行下一个进程 | 1. 无法并发，只能串行 2. 进程阻塞所带来的 CPU 时间浪费 |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| 多进程/线程时代 | 一个线程阻塞， cpu 可以立刻切换到其他线程中去执行            | 1. 进程/线程占用内存高 2. 进程/线程上下文切换成本高    |
| 协程时代        | 协程（用户态线程）**绑定线程**（内核态线程），cpu调度线程执行 | 1. 实现起来较复杂，协程和线程的绑定依赖调度器算法      |

### 协程和线程的映射关系

#### 1:1关系

上下文切换成本高，创建、删除和切换都由CPU完成

#### N:1关系

1个进程中的所有线程对应1个内核线程

- 上下文切换成本低，**在用户态即可完成协程切换**
- 无法利用多核CUP的加速功能
- 一旦协程阻塞，造成线程阻塞，本线程的其它协程无法执行

#### N:M关系

M个线程对应N个内核线程，解决了上述问题，但实现复杂

### GM模型

- **全局锁竞争**：没有本地队列，所有M都需要访问全局队列获取队列锁，导致激烈的锁竞争
- **M 转移 G 增加额外开销**，当 M1 在执行 G1 的时候， M1 创建了 G2，为了继续执行 G1，需要把 G2 保存到全局队列中，无法保证G2是被M1处理。因为 M1 原本就保存了 G2 的信息，所以 G2 最好是在 M1 上执行，这样的话也不需要转移G到全局队列和线程上下文切换
- **线程使用效率不能最大化**，没有**work-stealing** 和**hand-off** 机制

### GMP含义

- `G（Goroutine）`：代表Go 协程Goroutine，存储了 **Goroutine 的执行栈信息、Goroutine 状态以及 Goroutine 的任务函数等。**
- `M（Machine）`：Go 对操作系统线程的封装，可以看作操作系统内核线程。在绑定有效的 P 后，进入一个循环调度，不断从P的本地队列和全局队列中获取G执行。
- `P（Processor）`：虚拟处理器，**包含goroutine运行所需的上下文环境**。它有任务时，需要创建或唤醒一个系统线程处理它的队列中的任务。P的数量决定同时执行的任务的数量，`GOMAXPROCS`限制系统线程执行用户层面的任务的数量。

### 调度过程/原理

![img](assets/13.jpeg)

- 首先创建一个G放到本地队列中，满了就将本地队列中的一半G和新创建的G放到全局队列中

- 这时P会唤醒休眠的M或者新的M被创建，这个M会寻找一个空闲的P与自己绑定。然后M执行一个循环调度不断获取G并执行

- M从P的本地队列中获取G并执行，就执行**work stealing**机制

- M执行G时，若发生**阻塞**

  - 如果是**系统调用阻塞**，G,M都阻塞，会执行**hand off**机制。

  - 如果是G发生了IO阻塞，G协程挂起然后执行本地队列其他协程

#### **抢占**式调度

在coroutine中要等待一个协程主动让出CPU才执行下一个协程，在Go中，一个goroutine最多占用CPU 10ms，防止其他goroutine被饿死，这就是goroutine不同于coroutine的一个地方。

#### work stealing

当P的本地队列⽆可运⾏的G，就先加锁访问全局队列从中偷取，如果全局队列为空就尝试从其他M绑定的P偷取一半的G达到P之间的负载均衡

#### hand off

也称为**P分离机制**，当本线程 M 因为 G 进行的系统调用阻塞时，线程释放绑定的 P，把 P 转移给其他空闲的 M 执行，也提高了线程利用率（避免站着茅坑不拉shi）。

M执行G时，若发生**系统调用阻塞**，会阻塞G和M，此时P会和当前M解绑，并寻找新的M，如果没有空闲的M就会新建一个M，接管正在阻塞G所属的P，接着继续执行P中其余的G。如果M在执行G的过程发生网络IO等操作阻塞时（异步），阻塞G，**不会阻塞M**。M会寻找P中其它可执行的G继续执行

### P和M的数量问题

- **P的数量默认等于CPU核数**，由启动时环境变量 `$GOMAXPROCS` 或者由 `runtime` 包的方法 `GOMAXPROCS( )` 决定。P的数量决定同时执行的任务的数量
- **M的最大数量默认为10000个**，`runtime/deBug` 中的 `SetMaxThreads( )` 函数可设置M的最大数量

P与M 1:1进行绑定，但M的数量会略大于P的数量，**多出来的M将会在G产生系统调用时发挥作用**，当有M因为系统调用阻塞时，P与这个M解绑，空闲的M将获取这个P继续执行其中剩下的任务

### 本地队列窃取为什么不用加锁？

P从本地队列取G的这个操作，是一个 CAS 操作，**它具有原子性，是由硬件直接支持的，没有并发的竞争关系。**

### goroutine切换的时候上下文环境放在哪里

P中

## GC

### 基础概念

**赋值器**：说白了就是你写的程序代码，在程序的执行过程中，可能会改变对象的引用关系，或者创建新的引用。

**回收器**：垃圾回收器的责任就是去干掉那些程序中不再被引用得对象

**STW**（stop the word）：GC期间暂停应用程序的执行。

**root对象**：根对象是指不需要通过其他对象就可以直接访问到的对象，通过Root对象, 可以追踪到其他存活的对象。比如全局变量，执行栈

### **v1.3 标记-清除法**

第一步就是SWT，标记清除法的**最大弊端就是在整个GC期间需要STW，将整个程序暂停**。因为如果不进行STW的话，会出现已经被标记的对象A，引用了新的未被标记的对象B，但由于对象A已经标记过了，不会再重新扫描A对B的可达性，从而将B对象当做垃圾回收掉，这就是漏标问题。

GC算法优化的方向是什么，或者目标是什么，那就是让GC和用户程序可以互不干扰，并发进行。所以才有了后面的三色标记法。

### 并发问题解决

漏标问题：GC途中如果不SWT，由于**`黑色对象引用白色对象`并且`没有灰色对象间接引用这个白色对象`，导致白色对象无法被访问染色而被错误的回收**，通过破坏两个条件之一就可以解决这个问题，因此就有了

- **强三色不变性：强制性不允许黑色对象引用白色对象**
- **弱三色不变性：允许黑色对象引用白色对象，但是白色对象必须存在其他灰色对象对它的引用**

通过这两个规则就分别提出了两种实现机制：**插入写屏障和删除写屏障**。

#### **插入写屏障**

当一个对象引用另外一个对象时，将另外一个对象标记为灰色。（强三色不变式。不会存在黑色对象引用白色对象）

因为性能问题只在堆上使用，栈上不会使用，数十万goroutine的栈都进行屏障保护自然会有性能问题。由于栈上的对像没有插入写机制，在扫描完成后，仍然可能存在栈上的白色对象被黑色对象引用，所以在最后需要对栈上的空间进行STW，防止对象误删除，然后再rescan一次，重新标记新产生的引用

#### **删除写屏障**

**规则**：在删除引用时，如果被删除引用的对象自身为灰色或者白色，那么被标记为灰色。（满足弱三色不变式。灰色对象到白色对象的路径不会断）

在GC开始时，会扫描记录整个栈做快照，可以对栈对象引用删除写屏障

错标问题：但同样也存在着问题，就是如果没有其他的灰色或黑色对象引用这个白色对象，而删除这个引用将其标记成灰色，本来就是要回收的，但却错误的没有回收，降低了回收精度。**可以忽略，放到下一轮GC解决**



### **v1.5 三色标记法**

不通过STW就解决了并发GC的问题，引入了插入写屏障



### **v1.8 三色标记法+混合写屏障**

**混合写屏障**

GC刚开始的时候，会将栈上的可达对象全部标记为黑色。

GC期间，任何在栈上新创建的对象，均为黑色。

上面两点只有一个目的，将栈上的可达对象全部标黑，最后无需对栈进行STW，就可以保证栈上的对象不会丢失。有人说，一直是黑色的对象，那么不就永远清除不掉了么，这里强调一下，标记为黑色的是可达对象，不可达的对象一直会是白色，直到最后被回收。

堆上被删除的对象标记为灰色

堆上新添加的对象标记为灰色



## 反射
