# 其他

1.在定义结构体或变量时一般共用的单词放前面，便于自动补全找到。比如CodeSuccess, CodeInvalid, ParamSignUp, ParamLogin

2.编写中间件一般返回一个中间件函数，执行的函数方便传参数

3.循环导包问题，使用的变量方法放哪个包要准确把握

4.为什么user表中主键新加一个id字段，而不用user_id字段？

- 如果user_id作主键使用自增id那在该网站注册的人就可以根据user_id知道数据库中有多少个用户

- 当表中数据量过大时需分库，那不同库中的user_id可能重复

5.登录：先根据用户名查找用户，判断用户是否存在，再判断密码是否正确

6.服务端报错一般是不轻易暴露给外面的，直接返回CodeServerBusy

7.生成token时，要注意用于加盐的字符串长度要合适，可以在token.io进行测试

8.cld模式，controller层定义返回的信息的code和msg信息以及response方法。dao层为最终层，会产生很多错误返回，故定义errors

9.go语言内存对齐，在定义结构体字段时，类型相同的尽量放到一起

10.**解决传给前端数字id过大而失真的问题：前端用string存，结构体中json tag 加上string即可**

11.当前端返回一个0或nil数据时，如果参数使用了tag required就会报错

12.避免硬编码

13.在传递函数参数或返回变量时，尽量用指针，避免值拷贝

1.conn.read([]byte)

当conn不管是客户端还是服务端关闭后，都不会再阻塞，会继续往下执行

2.redis数据库中的key一定要与代码中对象的属性一样可以用tag改变在json中的名字

golang安装 www.jetbrains.com

破解 www.javatiku.cn/golang/11273.html

3.初始化全局变量

在函数中初始化全局变量时，千万不要用:=，不然是重新定义

## 组合

2024.9.13 如下所示接口嵌入，并未正正实现接口，所以b.f()调用会失败

```go
type A interface {
   f()
}

type B struct {
   A
}

type C struct {
}

func (c C) f() {
   fmt.Println("aaa")
}

func Test(t *testing.T) {
   var b B
   b.f()
}
```

```go
var (
	Db  *gorm.DB
	err error
)

func init() {
    //不能用:=
	Db, err = gorm.Open("mysql", config.Mysqldb)
	if err != nil {
		logger.Error(map[string]interface{}{"mysql connect err": err})
	}
	if Db.Error != nil {
		logger.Error(map[string]interface{}{"database err": Db.Error})
	}
}

```

4.gorm操作

gorm.DB的所有操作返回gorm.DB结构体。如果要判断操作是否成功，可以使用结构体中的Error变量

```go
err := dao.Db.Where("id = ?", id).First(&user).Error
```

14.使用golang编译器，当解析模板时，有时会遇到解析模板找不到文件，此时，不要使用goland的内置的run，此时，会保存到其他位置，只能手动编译，因为在解析模板的函数中，传递的参数值为相对路径，如果编译之后，保存到其他位置，相对路径则会失效。

# 切片

1.copy函数

```go
var temp [][]int
a2 := [][]int{{1,2,3},{4,5,6}}
//如下操作temp的每个切片仍是引用a2的切片
copy(temp, a2)
//每个元素都要copy，才是值传递
for i := range a2 {
    copy(temp[i], a2[i])
}
```

2.字符串str[:]

```go
str := "sdfdsf"
//str[:]仍是string类型不是[]byte切片
s := str[:]
//只能这样转换操作
s := []byte(str)
```



# 输入输出

### 1.scanf、scan、scanfln

scanf：按照给定的格式依次读取数据（包括非法数据），不能换行输入（如果要换行需要在前面加一个scanln吸收掉回车符，就像c语言中的getchar）。**会读入回车**

scan：比scanf高级，依次读取数据，**遇到回车会忽略**，可以换行输入（如果要先用了scan输入，再用scanf输入的话，需要在中间加一个scanln）

scanln：类似scan，但是遇到换行（回车）立马结束输入，如果要换行输入必须用多个scanln

### 2.输入带空格的字符串

```go
var s []byte
reader := bufio.NewReader(os.Stdin)
s, _, _ = reader.ReadLine()
```

# 数据库

## Redis

1.DENIED Redis is running in protected mode报错解决办法

```bash
config get protected-mode
"protected-mode"
"yes"
```

使用命令临时修改，CONFIG SET protected-mode no

打开配置conf文件，将protected-mode设置为no

## mysql

gorm使用中，查找单个数据时不要用Find，要用First，不然不会报gorm.ErrRecordNotFound的错误

# 环境

## terminal

idea中的Terminal窗口提示没有配置环境变量,但是cmd控制窗口启动正常

在setting->tool->Terminal中手动配置

## ide问题

**goland在使用的时候，控制台输出，如图所示，经常会出现一些意料之外的空白。**

依次点击【Help】、【Find Action】

搜【Registry】，并点击第一个选项，

将【go.run.processes.with.pty】 的勾给去了。

# leetcode

leetcode用go提交时所有测试用例共享全局变量

# 数据类型

1.在 Go 语言中，int 和 int32 不是同一个概念。

**在 32 位系统上，int 的大小为 32 位，而在 64 位系统上，int 的大小为 64 位。**int32 是一个有符号整数类型，其大小为 32 位，无论运行时环境的位数是多少。

# docker

docker desktop使用kubenetes国内安装不了，https://github.com/AliyunContainerService/k8s-for-docker-desktop/tree/v1.30.2查看教程下载

下载上面的zip文件解压后，在powershell中执行.\load_images.ps1，等待安装完成后，退出Docker Desktop，删除c盘用户目录下/.kube/config文件，然后再运行Docker Desktop

# 端口问题

```
1.停止Windows NAT 驱动程序
net stop winnat
2.开启Windows NAT 驱动程序
net start winnat
```
