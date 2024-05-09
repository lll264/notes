**r.POST("/contact/addFriend", controller.AddFriend)问题**

1.redis默认绑定127.0.0.1，如要绑定特定ip地址就要改变配置文件

`config set protected-mode no`



![image-20240424115723326](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240424115723326.png)

![image-20240424122155508](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240424122155508.png)



## 文件上传

将服务端发送的文件，如图片、音频、视频等文件保存到服务器转送

```go
// Upload 上传文件
func Upload(c *gin.Context) {
   srcFile, head, err := c.Request.FormFile("file")
   if err != nil {
      utils.RespFail(c.Writer, err.Error())
      return
   }
   suffix := ".png"
   ofilName := head.Filename
   tem := strings.Split(ofilName, ".")
   if len(tem) > 1 {
      suffix = "." + tem[len(tem)-1]
   }
   fileName := fmt.Sprintf("%d%04d%s", time.Now().Unix(), rand.Int32(), suffix)
   dstFile, err := os.Create("./asset/upload/" + fileName)
   if err != nil {
      utils.RespFail(c.Writer, err.Error())
      return
   }
   _, err = io.Copy(dstFile, srcFile)
   if err != nil {
      utils.RespFail(c.Writer, err.Error())
      return
   }
   //
   url := "./asset/upload/" + fileName
   utils.RespOK(c.Writer, "发送图片成功", url)
}
```

## 语音发送

与直接发送消息类似，主要通过前端实现。在浏览器录入语音发送到服务器通过upload上保存到服务器，发送到对应的用户

## 根据名字添加好友

```go
r.POST("/contact/addFriend", controller.AddFriend)
```

cottroler

```go
// AddFriend
// @Summary 添加好友
// @Tags 用户模块
// @param id formData string false "userID"
// @param name formData string false "targetID"
// @Success 200 {string} json{"code","message"}
// @Router /contact/addFriend [post]
func AddFriend(c *gin.Context) {
	//获取userID和targetID
	str := c.PostForm("userID")
	userID, _ := strconv.Atoi(str)
	str = c.PostForm("targetID")
	targetID, _ := strconv.Atoi(str)
	if userID == targetID {
		utils.RespFail(c.Writer, "不能添加自己为好友")
		return
	}
	//添加数据库库
	code := models.AddFriend(uint(userID), uint(targetID))
	if code == 0 {
		utils.RespOK(c.Writer, "添加好友成功", code)
	} else if code == 1 {
		utils.RespFail(c.Writer, "已添加该用户为好友")
	} else {
		utils.RespFail(c.Writer, "添加好友失败")
	}
}
```

model层

```go
// AddFriend 添加好友
func AddFriend(userId uint, targetId uint) int {
	user := UserBasic{}
	if targetId != 0 {
		user = FindByID(targetId)
		if user.ID != 0 {
			//判断是否已为好友
			temp := FindContact(userId, targetId)
			if temp.ID != 0 {
				return 1 //好友已存在
			}
			//开启事务添加两条记录
			tx := utils.DB.Begin()
			//事务一旦开始，不论什么异常都会RollBackx
			defer func() {
				if r := recover(); r != nil {
					tx.Rollback()
				}
			}()
			var contact, contact2 Contact
			contact.OwnerId = userId
			contact.TargetId = targetId
			contact.Type = 1
			if err := utils.DB.Create(&contact).Error; err != nil {
				tx.Rollback()
				return -1
			}
			contact2.OwnerId = targetId
			contact2.TargetId = userId
			contact2.Type = 1
			if err := utils.DB.Create(&contact2).Error; err != nil {
				tx.Rollback()
				return -1
			}
			tx.Commit()
			return 0 //成功
		}
		return -1 //user不存在 失败
	}
	return -1 //userID=0 失败
}
```

## 群聊

数据库表：community

### 创建群

```go
r.POST("/contact/createCommunity", controller.CreateCommunity)
```

```go
// CreateCommunity
// @Summary 创建群
// @Tags 用户模块
// @param ownerId formData string false "ownerId"
// @param name formData string false "name"
// @Success 200 {string} json{"code","message"}
// @Router /contact/createCommunity [post]
func CreateCommunity(c *gin.Context) {
   //获取userID和targetID
   ownerId, _ := strconv.Atoi(c.PostForm("ownerId"))
   name := c.PostForm("name")
   community := models.Community{
      OwnerId: uint(ownerId),
      Name:    name,
   }
   //添加数据库库
   code, msg := models.CreateCommunity(community)
   if code == 0 {
      utils.RespOK(c.Writer, msg, code)
   } else {
      utils.RespFail(c.Writer, msg)
   }
}
```

```go
// CreateCommunity 创建群
func CreateCommunity(community Community) (int, string) {
	tx := utils.DB.Begin()
	if len(community.Name) == 0 {
		return -1, "群名称不能为空"
	}
	if community.OwnerId == 0 {
		return -1, "请先登录"
	}
	if err := tx.Create(&community).Error; err != nil {
		tx.Rollback()
		return -1, "建群失败"
	}

	//群主加入该群，添加群关系
	contact := Contact{
		OwnerId:  community.OwnerId,
		TargetId: community.ID,
		Type:     2,
	}
	if err := tx.Create(&contact).Error; err != nil {
		tx.Rollback()
		return -1, "添加群关系失败"
	}
	tx.Commit()
	return 0, "建群成功"
}
```

### 加载当前用户所有群聊



### 加入群聊

页面穿透问题：前端一次点击，进行了两次请求



### 群发消息

不能发给自己



## 心跳机制

通知服务器，客户端存活状态，一旦服务器监测到在某段时间没有收到客户端发来的心跳包，服务器就会**释放曾经为此客户端分配的所有资源**，例如Socket连接

- 前端发送心跳信息更新后端连接的心跳时间，防止超时
- 后端每隔一段时间间隔就判断所有websocket长连接是否超时，超时就关闭

### 后端检测下线



### 前端发送心跳信息更新后端节点心跳时间



## 在线用户/消息缓存

重点：两个用户双发互相发送的消息共用一个key

发送消息时，会先在用户缓存中查看当前用户是否在线，在线就直接发送，不在线就要先将消息存到缓存中。

zset有序集合存消息，为了使消息不重复，增加一个createTime字段

分数也用createTime，从小到大排序



## 缓存消息推送



# 性能调优

OOS存储图片

# 发布部署

bat命令

使用docker部署项目



