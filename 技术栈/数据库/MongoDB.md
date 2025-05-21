# MongoDB基本学习

## 概念

MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

它支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

MongoDB服务端可运行在Linux、Windows平台，支持32位和64位应用，默认端口为27017。
推荐运行在64位平台，因为MongoDB在32位模式运行时支持的最大文件尺寸为2GB。

## 结构与特点

### 文档

**`MongoDB`中的记录是一个文档，它是由字段和值对组成的数据结构。**多个键及其关联的值有序地放在一起就构成了文档。

`MongoDB`文档类似于`JSON`对象。字段的值可以包括其他文档，数组和文档数组。

![在这里插入图片描述](D:\E\学习\Go\笔记\技术栈\数据库\assets\68af6e5064f5dc8e5693bd4cacd973ff.png)

**使用文档存储的优势：**

- 动态模式支持流畅的多态性
- 嵌入式文档和数组减少了对昂贵连接的需求
- 文档（即对象）对应于许多编程语言中的本机数据类型

### 集合

**集合就是一组文档，类似于关系数据库中的表。**

集合是无模式的，集合中的文档可以是各式各样的。例如，`{“hello,word”:“Mike”}`和`{“foo”: 3}`，它们的键不同，值的类型也不同，但是它们可以存放在同一个集合中，也就是不同模式的文档都可以放在同一个集合中。

> 为什么不直接将所有文档都放到一个集合中

这是因为所有文档都放在同一个集合中，无论对于开发者还是管理员，都很难对集合进行管理，而且这种情形下，对集合的查询等操作效率都不高。**所以在实际使用中，往往将文档分类存放在不同的集合中。**

可以使用“.”按照命名空间将集合划分为子集合。例如，对于一个博客系统，可能包括blog.user 和blog.article 两个子集合，这样划分只是让组织结构更好一些，blog 集合和blog.user、blog.article 没有任何关系。虽然子集合没有任何特殊的地方，但是**使用子集合组织数据结构清晰，这也是MongoDB 推荐的方法。**

### 数据库

**MongoDB 中多个文档组成集合，多个集合组成数据库。**

一个MongoDB 实例可以承载多个数据库。它们之间可以看作相互独立，每个数据库都有独立的权限控制。在磁盘上，不同的数据库存放在不同的文件中。

MongoDB 中存在以下系统数据库。

- **Admin 数据库**：一个权限数据库，如果创建用户的时候将该用户添加到admin 数据库中，那么该用户就自动继承了所有数据库的权限。
- **Local 数据库**：这个数据库永远不会被复制，可以用来存储本地单台服务器的任意集合。
- **Config 数据库**：当MongoDB 使用分片模式时，config 数据库在内部使用，用于保存分片的信息。

### 总结

**一个MongoDB 实例可以包含一组数据库，一个DataBase 可以包含一组Collection（集合），一个集合可以包含一组Document（文档）。**

一个Document包含一组field（字段），每一个字段都是一个key/value pair

- key: 必须为字符串类型

- value：可以包含如下类型

  - 基本类型，例如，string，int，float，timestamp，binary 等类型

  - 一个document

  - 数组类型

## 应用场景

传统的关系型数据库(如MySQL)，在数据操作的三高需求以及应对Web2.0的网站需求面前，显得力不从心，而 MongoDB可应对“三高“需求

- High performance：对数据库**高并发读写的需求**

- Huge Storage：对**海量数据的高效率存储和访问的需求**

- High Scalability && High Availability：**对数据库的高可扩展性和高可用性的需求**


# MongoDB操作

## 数据库相关

```bash
# 查看所有数据库
show databases

# 选择数据库(如果数据库不存在,不会报错;会隐式创建:当后期该数据库有数据时自动创建)
use 数据库名

# 删除数据库(先选中数据库)
db.dropDatabase()
```

## 集合相关

```bash
# 查看所有集合
show collections

# 创建集合(插入数据会隐式创建)
db.createCollection('集合名')

# 删除集合
db.集合名.drop()
```

## 文档CRUD

### 插入

#### 普通插入

```
db.集合名.insert(json数据)
db.test_col.insert({"name":"zs","age":18})
```

- 集合存在则直接插入数据，不存在则隐式创建集合并插入数据
- mongodb会自动给每条数据创建全球唯一的`_id`键（我们也可以自定义`_id`的值，只要给插入的json数据增加`_id`键即可覆盖，但是不推荐这样做）

![image-20250519154017400](D:\E\学习\Go\笔记\技术栈\数据库\assets\image-20250519154017400.png)

#### 批量插入

利用for循环插入数据

```
for(var i=1;i<10;i++){
	db.student.insert({name:"a"+i,age:i})
}
```

直接插入json数组

```
db.c1.insert([{uname:"z3", age:3},{uname:"z4", age:4},{uname:"w5", age:5} ])
```



### 删除

```bash
#true 只删除一条
#false 删除多条 默认全部删除
db.集合名.remove(条件[,是否删除一条])
db.student.remove({}, true)
```



### 修改

```bash
db.集合名.update(条件, 新数据 [,是否新增, 是否修改多条])

# 新数据
- 默认是对原数据进行替换，原数据部分字段会消失
- 若要进行修改,格式为 {修改器:{key:value}}

# 是否新增
- 条件匹配不到数据时是否插入: true插入,false不插入(默认)

# 是否修改多条
- 条件匹配成功的数据是否都修改: true都修改,false只修改一条(默认)

```

| **修改器** | **作用** |
| ---------- | -------- |
| $inc       | 递增     |
| $rename    | 重命名列 |
| $set       | 修改列值 |
| $unset     | 删除列   |

#### 案例

使用修改器将{name:“zsr3”}更改为{name:“zsr3333”}

```
db.people.update({name:"zsr3"},{$set:{name:"zsr3333"}})
```

给{name:“zsr10”}的年龄增加或减少2岁

```
# 增加两岁
db.people.update({name:"zsr10"},{$inc:{age:2}})

# 减少两岁
db.people.update({name:"zsr10"},{$inc:{age:-2}})
```

### 查询

```bash
db.集合名.find(条件 [,查询的列])
db.集合名.find(条件 [,查询的列]).pretty()	#格式化查看

# 条件
- 查询所有数据	{}或不写
- 查询指定要求数据	{key:value}或{key:{运算符:value}}

# 查询的列(可选参数)
- 不写则查询全部列
- {key:1}	只显示key列
- {key:0}	除了key列都显示
- 注意:_id列都会存在

```

| **运算符** | **作用** |
| ---------- | -------- |
| $gt        | 大于     |
| $gte       | 大于等于 |
| $lt        | 小于     |
| $lte       | 小于等于 |
| $ne        | 不等于   |
|            | in       |
| $nin       | not in   |

#### 案例

只显示name列

```
db.student.find({},{name:1})
```

显示除了name列以外的数据

```
db.student.find({},{name:0})
```

根据条件查询

```bash
#查询age>21的数据
db.student.find({age:{$gt:21}})
#查询age=21的数据
db.student.find({age:21})
#查询age等于1\2\3的数据
db.student.find({age:{$in:[1,2,3]}})
```

## 聚合查询

## 索引

# Go操作MongoDB

## 连接

```go
import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

var db *mongo.Database

func initMongo() {
	// 设置客户端连接配置
	clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")

	// 连接到MongoDB
	client, err := mongo.Connect(context.Background(), clientOptions)
	if err != nil {
		fmt.Println(err)
	}
	// 检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println("Connected to MongoDB!")
	//操作指定的数据库
	db = client.Database("test_db")
}
```

## CRUD

### 新增文档

#### 插入单个

```go
// InsertTestUser 插入单个文档
func InsertTestUser() {
    user := User{"zs", 18}
    res, err := db.Collection("test_user").InsertOne(context.TODO(), user)
    if err != nil {
       panic(err)
    }
    fmt.Println(res.InsertedID)
}
```

#### 插入多个

```go
// BatchInsertTestUser 批量插入多个文档
func BatchInsertTestUser() {
    users := []interface{}{
       User{"zs", 18},
       User{"ls", 19},
       User{"ww", 20},
    }
    res, err := db.Collection("test_user").InsertMany(context.TODO(), users)
    if err != nil {
       fmt.Println(err)
    }
    fmt.Println(res.InsertedIDs)
}
```

### 删除文档

```go
func DeleteTestUser() {
	//删除一个文档
	filter := bson.M{"name": "zs"}
	res, err := db.Collection("test_user").DeleteOne(context.TODO(), filter)
	//删除多个文档
	//res, err := db.Collection("test_user").DeleteMany(context.TODO(), filter)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(res.DeletedCount)
}
```



### 修改文档

```go
// UpdateTestUser 更新一个文档
func UpdateTestUser(age int) {
    //先定义一个过滤器筛选要更新的文档
    filter := bson.M{"name": "zs"}
    //实现更新的操作，设置指定元素的age字段为20
    update := bson.M{"$set": bson.M{"age": age}}
    _, err := db.Collection("test_user").UpdateOne(context.TODO(), filter, update)
    if err != nil {
       fmt.Println(err)
    }
}
```

### 查询文档

#### 查询单个数据

```go
// FindTestUser 普通条件查询
func FindTestUser() {
    var result User
    //设置查询条件
    filter := bson.M{"name": "zs"}
    err := db.Collection("test_user").FindOne(context.TODO(), filter).Decode(&result)
    if err != nil {
       fmt.Println(err)
    }
    fmt.Println(result)
}
```

#### 查询多个数据

```go
// FindTestUser2 查询多条数据
func FindTestUser2() {
    //设置查询条件
    filter := bson.M{}
    cursor, err := db.Collection("test_user").Find(context.TODO(), filter)
    if err != nil {
       fmt.Println(err)
    }
    defer cursor.Close(context.TODO())
    //解析查询数据
    var users []User
    if err = cursor.All(context.TODO(), &users); err != nil {
       panic(err)
    }
    fmt.Println(users)
}
```

#### 投影查询

#### limit和offset

#### order by desc

#### 比较查询

#### 包含查询

#### and查询