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

## leetcode

leetcode用go提交时所有测试用例共享全局变量

## 端口问题

```
1.停止Windows NAT 驱动程序
net stop winnat
2.开启Windows NAT 驱动程序
net start winnat
```

### 解决端口占用问题

```bash
#查看端口占用的线程
netstat -aon|findstr "13800" 
#根据进程号杀死进程
taskkill /f /pid [进程号]
```

#### 重启[NAT](https://so.csdn.net/so/search?q=NAT&spm=1001.2101.3001.7020)网络

如果没有发现占用端口的进程，可以尝试重启NAT网络。

1. 管理员模式打开cmd
2. 依次输入代码

```shell
net stop winnat

net start winnat
```

# golang

## 切片

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



## 输入输出

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

## 数据类型

1.在 Go 语言中，int 和 int32 不是同一个概念。

**在 32 位系统上，int 的大小为 32 位，而在 64 位系统上，int 的大小为 64 位。**int32 是一个有符号整数类型，其大小为 32 位，无论运行时环境的位数是多少。

## 面向对象

1.对象为nil时调用出错

因为f函数接收者为一个实例，因此用指针调用时必须指向一个P对象实例，不能为nil

```go
type P struct {
}

func (p P) f() {
}

func Test22(t *testing.T) {
    var p *P
    p.f()
}
```



## gorm的坑

### 1 连接

连接创建数据，自定义连接表结构

SetupJoinTable指定的模型的位置不一样，建立的关联表也有区别

```
if err := DB.SetupJoinTable(&test2.Issue{}, "Collaborators", &IssueCollaborator{}); err != nil {
```

### 2 零值更新

如果使用模型实体类的方式去更新字段，该字段需要是非零值，否则不会更新该字段。可以使用map或者直接用指针。

### 3.update

db.Model(&user).updates(updateColums)

更新后user数据会更新

### parse time

要将表中的时间比如 timestamp 类型的数据取到结构体中的time.Time类型中，就要在连接数据库时指定 parseTime=True

### 取地址&

操作对象时必须要取地址，不然会报错

unreadable: invalid interface type

```go
func GetUserByName(username string) (user *User, err error) {
	err = db.Where("username = ?", username).First(&user).Error
	return
}
```

### 关联查询

#### 结果映射

ActivitySign作为映射的结果，不能够使用gorm:"-"tag标记，比如ActivityName和UserName是外表的数据，不然无法获取到对应select的数据

gorm:"-"tag标记表示不参与映射，初始化是也不会创建对应表的列

```go
type ActivitySign struct {
	Id           int64  `gorm:"column:id" json:"id,omitempty"`
	ActivityId   int64  `gorm:"column:activity_id" json:"activityId,omitempty"`
	UserId       int64  `gorm:"column:user_id" json:"userId,omitempty"`
	Time         string `gorm:"column:time" json:"time,omitempty"`
	ActivityName string `json:"activityName,omitempty"`
	UserName     string `json:"userName,omitempty"`
}
```



```go
func GetActivitySignByPage(a *ActivitySign, page, size int) (list []*ActivitySign, err error) {
	query := db.Model(&ActivitySign{}).Select("activity_sign.*, activity.name as ActivityName, user.name as UserName").
		Joins("left join activity on activity_sign.activity_id = activity.id").
		Joins("left join user on activity_sign.user_id = user.Id")
	err = query.Limit(size).Offset((page - 1) * size).Find(&list).Error
	return
}
```

## gin框架

### ShouldBindQuery

ShouldBind 如果要绑定查询参数到结构体需要指定form tag标记

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

### 启动mysql

本地mysql启动net start MySQL服务名无效

**错误原因是：**因为net start +服务名，启动的是win下注册的服务。此时，系统中并没有注册mysql到服务中。即当前路径下没有mysql服务。

**解决办法：**1. 来到MySQL的安装路径下bin

​            2. 在命令行中输入mysqld --install（成功：出现Service successfully install代表你已经安装成功，）



gorm使用中，查找单个数据时不要用Find，要用First，不然不会报gorm.ErrRecordNotFound的错误

### 不指定CreateAt创建对象

出现错误 Error Code: 1364. Field ‘createdAt‘ doesn‘t have a default value。但是正常gorm会默认填充createAt为当前时间



# 环境

## terminal

idea中的Terminal窗口提示没有配置环境变量,但是cmd控制窗口启动正常

在setting->tool->Terminal中手动配置

## ide问题

**goland在使用的时候，控制台输出，如图所示，经常会出现一些意料之外的空白。**

依次点击【Help】、【Find Action】

搜【Registry】，并点击第一个选项，

将【go.run.processes.with.pty】 的勾给去了。

## 依赖问题

在golang编译器中`Environment` 处填写的 GOPROXY 网址要与cmd命令行输入 `go env` 中的 GOPROXY 保持一致。不然会出现import爆红的情况。

# docker

## 1 安装

docker desktop使用kubenetes国内安装不了，https://github.com/AliyunContainerService/k8s-for-docker-desktop/tree/v1.30.2查看教程下载

下载上面的zip文件解压后，在powershell中执行.\load_images.ps1，等待安装完成后，退出Docker Desktop，删除c盘用户目录下/.kube/config文件，然后再运行Docker Desktop

## 2 Virtual Machine Platform not enabled

Docker启动报错virtual machine platform not enabled。原因是windows未启动虚拟化平台。

跟着下面的操作基本都能解决问题。

1.打开控制面板

![img](D:\E\学习\Go\笔记\assets\db03e7fbe9b94573aaa769f4a89499c2.png)

2.在启用或关闭windows功能中开启Hyper-V和虚拟机平台

 ![img](D:\E\学习\Go\笔记\assets\68214d1b3dc249ab83602c7d32eeceaf.png)

这个虚拟机平台也一定要勾选，我看晚上很多帖子是没说需要勾选这个的。但有些安卓模拟器会给你把他取消掉，所以如果发现自己看过很多帖子都解决不掉记得来看看虚拟机平台有没有勾选。

重启系统。

# git

## ssh连接失败

```
git push
ssh: connect to host github.com port 22: Connection refused
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

user目录下.ssh目录新建文件config，并复制如下内容

```
Host github.com
User xxxxqq.com
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
```

保存后ssh连接成功

```
ssh -T git@github.com
```

git push也可以正常推送



# windows

## 1 远程计算机或设备将不接收连接

网络用不了

![3a9e374d74994fde20c76039717493f3](D:\E\学习\Go\笔记\assets\3a9e374d74994fde20c76039717493f3.jpeg)

## 2 应用开机自启关闭

直接右键禁用即可

![image-20250327192631078](D:\E\学习\Go\笔记\assets\image-20250327192631078.png)

3.手机如何访问电脑上的web项目

通过无线局域网就可以直接访问，通过cmd窗口ipconfig命令查看无线局域网ip，web项目要监听这个地址。

一般来说只要连接同一个wife使用无线局域网WLAN IP 即可访问，但是我必须要连热点，使用无线局域网本地连接IP才行。
