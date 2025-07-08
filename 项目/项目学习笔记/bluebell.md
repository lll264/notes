雪花算法库

```go
import (
	"fmt"
	"time"

	"github.com/bwmarrin/snowflake"
)

var node *snowflake.Node

func Init(startTime string, machineId int64) (err error) {
	var st time.Time
	st, err = time.Parse("2006-01-02", startTime)
	if err != nil {
		return
	}
	snowflake.Epoch = st.UnixNano() / 1000000
	node, err = snowflake.NewNode(machineId)
	return
}
func GenID() int64 {
	return node.Generate().Int64()
}

func main() {
	if err := Init("2020-07-01", 1); err != nil {
		fmt.Println("init err:", err)
		return
	}
	id := GenID()
	fmt.Println(id)
}
```



logger日志

输出到文件或终端

问题

1.viper.Unmarshal(Conf)解析不出数据

原因：配置文件app:和结构体AppConfig不对应，将配置文件中的app:删除即可

```yaml
app: 
    name: "web_app"
    mode: "dev"
    port: 8080
    version: "v0.0.1"
    start_time: "2020-07-01"
    machine_id: 1
```



# 功能需求



# 基本骨架建立

初始化viper到结构体变量conf，并监视后续配置文件的变化

初始化zap日志库获取*zap.logger并替换成用zap.L()调用

配置mysql、配置redis

使用channel实现优雅关机。当关闭服务端时，会等待5秒直到当前的服务执行完后才关闭服务。

```go
func main() {
	//1.加载配置
	if err := settings.Init("conf/config.yaml"); err != nil {
		fmt.Println("init settings err:", err)
		return
	}
	//2.初始化日志
	if err := logger.Init(settings.Conf.LogConfig, settings.Conf.Mode); err != nil {
		fmt.Println("init logger err:", err)
		return
	}
	// Sync调用底层Core的Sync方法，刷新所有缓冲的日志条目。应用程序在退出之前应该注意调用Sync。
	// 在程序退出之前，把缓冲区里的日志刷到磁盘上
	defer zap.L().Sync()
	//3.初始化MySQL连接
	if err := mysql.Init(settings.Conf.MySQLConfig); err != nil {
		fmt.Println("init mysql err:", err)
		return
	}
	//关闭db
	defer mysql.Close()
	//4.初始化Redis连接
	if err := redis.Init(settings.Conf.RedisConfig); err != nil {
		fmt.Println("init redis err:", err)
		return
	}
	//关闭rdb
	defer redis.Close()

	if err := snowflake.Init(settings.Conf.StartTime, settings.Conf.MachineID); err != nil {
		fmt.Println("init snowflake err:", err)
		return
	}

	//初始化gin框架内置校验器使用的翻译器
	if err := controller.InitTrans("zh"); err != nil {
		fmt.Println("init trans err:", err)
		return
	}
	//注册路由
	r := router.SetUpRouter(settings.Conf.Mode)
	//启动服务(优雅关机)
	srv := &http.Server{
		Addr:    fmt.Sprintf(":%d", settings.Conf.Port),
		Handler: r,
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
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM) // 此处不会阻塞
	<-quit                                               // 阻塞在此，当接收到上述两种信号时才会往下执行
	zap.L().Info("Shutdown Server ...")
	// 创建一个5秒超时的context
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	// 5秒内优雅关闭服务（将未处理完的请求处理完再关闭服务），超过5秒就超时退出
	if err := srv.Shutdown(ctx); err != nil {
		zap.L().Fatal("Server Shutdown: ", zap.Error(err))
	}

	zap.L().Info("Server exiting")
}
```

# 用到的库

## 1.viper读取配置

### config.yaml文件

首先在conf目录下创建一个config.yaml文件存所有配置

```yaml
name: "web_app"
mode: "dev"
port: 8080
version: "v0.0.1"
start_time: "2020-07-01"
machine_id: 1

auth:
  jwt_expire: 8760
log:
  level: "debug"
  filename: "web_app.log"
  max_size: 200
  max_age: 30
  max_backups: 7
mysql:
  host: "127.0.0.1"
  port: 3306
  user: "root"
  password: "123"
  dbname: "bluebell"
  max_open_conns: 200
  max_idle_conns: 10
redis:
  host: "127.0.0.1"
  port: 6379
  password: ""
  db: 0
  pool_size: 100

```

### 初始化并读取配置

在viper.go中创建AppConfig结构体用于保存从config.yaml中读取的配置信息。

通过`viper.SetConfigFile(fileName)`指定配置文件路径，`viper.ReadInConfig()`读取配置文件信息。

通过`viper.Unmarshal(Conf)`将viper中的配置信息反序列化到结构体变量conf中。至此，就完成了配置信息的读取工作。

还有一个重要的工作就是，当修改配置文件后，要重新读取配置。可以监控配置文件变化，`viper.WatchConfig()`。当配置文件发生变化时，会调用会调函数，在回调函数中就可以重新反序列化配置信息到AppConfig结构体变量中。

```go
package settings

import (
	"fmt"
	"github.com/fsnotify/fsnotify"
	"github.com/spf13/viper"
)

// Conf 全局变量，用来保存程序的所有配置信息
var Conf = new(AppConfig)

type AppConfig struct {
	Name      string `mapstructure:"name"`
	Mode      string `mapstructure:"mode"`
	Port      int    `mapstructure:"port"`
	Version   string `mapstructure:"version"`
	StartTime string `mapstructure:"start_time"`
	MachineID int64  `mapstructure:"machine_id"`

	*LogConfig   `mapstructure:"log"`
	*MySQLConfig `mapstructure:"mysql"`
	*RedisConfig `mapstructure:"redis"`
}

type MySQLConfig struct {
	User         string `mapstructure:"user"`
	Password     string `mapstructure:"password"`
	Host         string `mapstructure:"host"`
	Port         int    `mapstructure:"port"`
	DB           string `mapstructure:"dbname"`
	MaxOpenConns int    `mapstructure:"max_open_conns"`
	MaxIdleConns int    `mapstructure:"max_idle_conns"`
}

type RedisConfig struct {
	Host         string `mapstructure:"host"`
	Password     string `mapstructure:"password"`
	Port         int    `mapstructure:"port"`
	DB           int    `mapstructure:"db"`
	PoolSize     int    `mapstructure:"pool_size"`
	MinIdleConns int    `mapstructure:"min_idle_conns"`
}

type LogConfig struct {
	Level      string `mapstructure:"level"`
	Filename   string `mapstructure:"filename"`
	MaxSize    int    `mapstructure:"max_size"`
	MaxAge     int    `mapstructure:"max_age"`
	MaxBackups int    `mapstructure:"max_backups"`
}

// Init 初始化viper
func Init(fileName string) (err error) {
	//方式1：直接指定配置文件路径（相对路径或绝对路径）
	//viper.SetConfigFile("D:\\Go\\project\\bluebell\\config.yaml")
	//viper.SetConfigFile("conf/config.yaml")

	//方式2：指定配置文件名和配置文件的位置，viper自行查找可用的配置文件
	//配置文件不需要带后缀，可配置多个
	//viper.SetConfigName("config")      //指定配置文件名称
	//viper.AddConfigPath(".") //指定查找配置文件的路径(这里使用相对路径)

	//基本上是配合远程配置中心使用的，告诉viper当前的数据使用说明格式去解析
	//viper.SetConfigType("yaml")

	viper.SetConfigFile(fileName)
	err = viper.ReadInConfig() //读取配置信息
	if err != nil {
		//读取配置信息失败
		fmt.Println("viper.ReadInConfig err:", err)
		return
	}

	//把读取到的配置信息反序列化到Conf变量中
	if err := viper.Unmarshal(Conf); err != nil {
		fmt.Println("viper.Unmarshal err:", err)
	}
	//fmt.Println(Conf)
	viper.WatchConfig()
	viper.OnConfigChange(func(in fsnotify.Event) {
		//重新反序列化
		if err := viper.Unmarshal(Conf); err != nil {
			fmt.Println("viper.Unmarshal err:", err)
		}
		fmt.Println("配置文件修改了")
	})
	return
}

```



## 2.zap日志库

### zap日志初始化

使用到的配置信息有：**日志级别（level）、日志文件路径（filename）、日志切割的配置（max_size、max_age、max_backups）**、**级别(mod)**

创建编码器encoder：格式化时间、日志级别大写、使用JSONEcoder

创建WriteSyncer：指定写入的文件的路径，使用日志切割

如果mode="dev"，就进入开发者模式。**创建并使用两个core，产生的日志同时输入到文件和终端。**

替换zap包中全局的logger实例，后续在其他包中只需使用zap.L()调用即可，而不是包名.log，比较简洁。

```go
package logger

import (
	"bluebell/settings"
	"net"
	"net/http"
	"net/http/httputil"
	"os"
	"runtime/debug"
	"strings"
	"time"

	"github.com/spf13/viper"

	"github.com/gin-gonic/gin"
	"github.com/natefinch/lumberjack"
	"go.uber.org/zap"
	"go.uber.org/zap/zapcore"
)

// Init 初始化Logger
func Init(cfg *settings.LogConfig, mode string) (err error) {
	writeSyncer := getLogWriter(
		cfg.Filename, cfg.MaxSize, cfg.MaxBackups, cfg.MaxAge)
	encoder := getEncoder()
	var l = new(zapcore.Level)
	err = l.UnmarshalText([]byte(viper.GetString("log.level")))
	if err != nil {
		return
	}
	var core zapcore.Core
	if mode == "dev" {
		//进入开发模式，日志输出到终端
		consoleEncoder := zapcore.NewConsoleEncoder(zap.NewDevelopmentEncoderConfig())
		core = zapcore.NewTee(
			zapcore.NewCore(encoder, writeSyncer, l),
			zapcore.NewCore(consoleEncoder, zapcore.Lock(os.Stdout), zap.DebugLevel),
		)
	} else {
		core = zapcore.NewCore(encoder, writeSyncer, l)
	}

	lg := zap.New(core, zap.AddCaller())
	// 替换zap包中全局的logger实例，后续在其他包中只需使用zap.L()调用即可
	zap.ReplaceGlobals(lg)
	zap.L().Info("init logger success")
	return
}

func getEncoder() zapcore.Encoder {
	encoderConfig := zap.NewProductionEncoderConfig()
	encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder
	encoderConfig.TimeKey = "time"
	encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
	encoderConfig.EncodeDuration = zapcore.SecondsDurationEncoder
	encoderConfig.EncodeCaller = zapcore.ShortCallerEncoder
	return zapcore.NewJSONEncoder(encoderConfig)
}

func getLogWriter(filename string, maxSize, maxBackup, maxAge int) zapcore.WriteSyncer {
	lumberJackLogger := &lumberjack.Logger{
		Filename:   filename,
		MaxSize:    maxSize,
		MaxBackups: maxBackup,
		MaxAge:     maxAge,
	}
	return zapcore.AddSync(lumberJackLogger)
}
```

### gin默认日志重写

gin.Default()获取的路由引擎默认使用了logger和recover两个中间件，我们要重写并使用这两个中间件，将其中使用了日志的部分全改为zap日志。

```go
// GinLogger 接收gin框架默认的日志
func GinLogger() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()
		path := c.Request.URL.Path
		query := c.Request.URL.RawQuery
		c.Next()

		cost := time.Since(start)
		zap.L().Info(path,
			zap.Int("status", c.Writer.Status()),
			zap.String("method", c.Request.Method),
			zap.String("path", path),
			zap.String("query", query),
			zap.String("ip", c.ClientIP()),
			zap.String("user-agent", c.Request.UserAgent()),
			zap.String("errors", c.Errors.ByType(gin.ErrorTypePrivate).String()),
			zap.Duration("cost", cost),
		)
	}
}

// GinRecovery recover掉项目可能出现的panic，并使用zap记录相关日志
func GinRecovery(stack bool) gin.HandlerFunc {
	return func(c *gin.Context) {
		defer func() {
			if err := recover(); err != nil {
				// Check for a broken connection, as it is not really a
				// condition that warrants a panic stack trace.
				var brokenPipe bool
				if ne, ok := err.(*net.OpError); ok {
					if se, ok := ne.Err.(*os.SyscallError); ok {
						if strings.Contains(strings.ToLower(se.Error()), "broken pipe") || strings.Contains(strings.ToLower(se.Error()), "connection reset by peer") {
							brokenPipe = true
						}
					}
				}

				httpRequest, _ := httputil.DumpRequest(c.Request, false)
				if brokenPipe {
					zap.L().Error(c.Request.URL.Path,
						zap.Any("error", err),
						zap.String("request", string(httpRequest)),
					)
					// If the connection is dead, we can't write a status to it.
					c.Error(err.(error)) // nolint: errcheck
					c.Abort()
					return
				}

				if stack {
					zap.L().Error("[Recovery from panic]",
						zap.Any("error", err),
						zap.String("request", string(httpRequest)),
						zap.String("stack", string(debug.Stack())),
					)
				} else {
					zap.L().Error("[Recovery from panic]",
						zap.Any("error", err),
						zap.String("request", string(httpRequest)),
					)
				}
				c.AbortWithStatus(http.StatusInternalServerError)
			}
		}()
		c.Next()
	}
}

```

## 3.jwt认证

1. 客户端携带用户的登录凭证（一般为用户名密码）提交请求
2. 服务端收到登录请求，验证凭证正确性，如果正确则按照协议规定生成token信息，经过签名并返回给客户端
3. 客户端收到token信息，可以保存在cookie或者其他地方，以后每次请求的时候都携带上token信息
4. 业务服务器收到请求，验证token的正确性，如果正确则进行下一步操作

登录成功后调用GenToken生成token字符串，后续每次请求都要带上token，并对其进行解析，解析成功后才能进行相关操作。

**生成和解析token的方法如下**(jwt.go)

```go
package jwt

import (
	"errors"
	"time"

	"github.com/golang-jwt/jwt/v4"
)

const TokenExpireDuration = time.Hour * 24 * 365

// CustomSecret 用于加盐的字符串
var mySecret = []byte("whatthefuck123weishenmebuneng123")

// MyClaims 自定义声明类型 并内嵌jwt.RegisteredClaims
// jwt包自带的jwt.RegisteredClaims只包含了官方字段
// 假设我们这里需要额外记录一个username字段，所以要自定义结构体
// 如果想要保存更多信息，都可以添加到这个结构体中
type MyClaims struct {
	// 可根据需要自行添加字段
	UserID               int64  `json:"user_id,omitempty"`
	Username             string `json:"username,omitempty"`
	jwt.RegisteredClaims        // 内嵌标准的声明
}

// GenToken 生成JWT
func GenToken(userID int64, username string) (string, error) {
	// 创建一个我们自己的声明
	claims := MyClaims{
		userID,
		username, // 自定义字段
		jwt.RegisteredClaims{
			ExpiresAt://jwt.NewNumericDate(time.Now().Add(time.Duration(viper.GetInt("auth.jwt_expire")))),
			jwt.NewNumericDate(time.Now().Add(TokenExpireDuration)),
			Issuer: "bluebell", // 签发人
		},
	}
	// 使用指定的签名方法创建签名对象
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	// 使用指定的secret签名并获得完整的编码后的字符串token
	return token.SignedString(mySecret)
}

// ParseToken 解析JWT
func ParseToken(tokenString string) (*MyClaims, error) {
	// 解析token
	// 如果是自定义Claim结构体则需要使用 ParseWithClaims 方法
	token, err := jwt.ParseWithClaims(tokenString, &MyClaims{}, func(token *jwt.Token) (i interface{}, err error) {
		// 直接使用标准的Claim则可以直接使用Parse方法
		//token, err := jwt.Parse(tokenString, func(token *jwt.Token) (i interface{}, err error) {
		return mySecret, nil
	})
	if err != nil {
		return nil, err
	}
	// 对token对象中的Claim进行类型断言
	if claims, ok := token.Claims.(*MyClaims); ok && token.Valid { // 校验token
		return claims, nil
	}
	return nil, errors.New("invalid token")
}

```

**在gin中，一般将jwt认证编写成一个中间件，在对应的路由都加上该中间件**

token一般放在请求头中以键值对方式存在，形如`Authorization: Bearer xxx`。

通过`c.Request.Header.Get("Authorization")`获取token。如果为空，表示前端未带上token还未登录。如果格式不对，表示参数有误。直到获取到了token再对其进行解析得到对应的MyClaims结构体。

解析成功就可以将其中的userId保存到上下文*gin.Context中，c.Next()执行后续中间件，后续也可以使用到userId。

```go
package middlewares

import (
	"bluebell/controller"
	"bluebell/pkg/jwt"
	"strings"

	"go.uber.org/zap"

	"github.com/gin-gonic/gin"
)

// JWTAuthMiddleware 基于JWT的认证中间件
func JWTAuthMiddleware() func(c *gin.Context) {
	return func(c *gin.Context) {
		// 客户端携带Token有三种方式 1.放在请求头 2.放在请求体 3.放在URI
		// 这里假设Token放在Header的Authorization中，并使用Bearer开头
		//Authorization: Bearer xxx
		// 这里的具体实现方式要依据你的实际业务情况决定
		authHeader := c.Request.Header.Get("Authorization")
		if authHeader == "" {
			controller.ResponseError(c, controller.CodeNeedLogin)
			c.Abort()
			return
		}
		// 按空格分割
		parts := strings.SplitN(authHeader, " ", 2)
		if !(len(parts) == 2 && parts[0] == "Bearer") {
			zap.L().Error("token of split invalid")
			controller.ResponseError(c, controller.CodeInvalidToken)
			c.Abort()
			return
		}
		// parts[1]是获取到的tokenString，我们使用之前定义好的解析JWT的函数来解析它
		mc, err := jwt.ParseToken(parts[1])
		if err != nil {
			zap.L().Error("jwt.ParseToken err", zap.Error(err))
			controller.ResponseError(c, controller.CodeInvalidToken)
			c.Abort()
			return
		}
		// 将当前请求的userID信息保存到请求的上下文c上
		c.Set(controller.CtxUserIDKey, mc.UserID)
		c.Next() // 后续的处理函数可以用过c.Get("userID")来获取当前请求的用户信息
	}
}

```



## 4.雪花算法

一个表中的主键 id 一般使用自增的方式，但是如果进行水平分表之后，多个表中会生成重复的 id 值。

雪花算法是其中一个用于解决分布式 id 的高效方案，每秒可生成百万个不重复 id

在snowflake包中定义如下

```go
package snowflake

import (
	"time"

	"github.com/bwmarrin/snowflake"
)

/*
	雪花算法生成id
*/

var node *snowflake.Node

func Init(startTime string, machineId int64) (err error) {
	var st time.Time
	st, err = time.Parse("2006-01-02", startTime)
	if err != nil {
		return
	}
	snowflake.Epoch = st.UnixNano() / 1000000
	node, err = snowflake.NewNode(machineId)
	return
}
func GenID() int64 {
	return node.Generate().Int64()
}
```



## 5.validator参数校验

在结构体中指定tap`binding`，绑定参数时会自动去进行参数的校验，例如`binding:"required"`参数不能为空，`binding:"required,eqfield=Password"`RePassword必须等于Password。

但是参数校验返回的错误error是英文，故需使用翻译器，将error翻译为中文后再返回给前端，使用removeTopStruct 去除提示信息中的结构体名称。

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	"github.com/go-playground/locales/en"
	"github.com/go-playground/locales/zh"
	ut "github.com/go-playground/universal-translator"
	"github.com/go-playground/validator/v10"
	enTranslations "github.com/go-playground/validator/v10/translations/en"
	zhTranslations "github.com/go-playground/validator/v10/translations/zh"
	"go.uber.org/zap"
	"reflect"
	"strings"
)

type ParamSignUp struct {
	Username   string `json:"username" binding:"required" form:"username"`
	Password   string `json:"password" binding:"required" form:"password"`
	RePassword string `json:"re_password" binding:"required,eqfield=Password" form:"re_password"`
}

// 定义一个全局翻译器T
var trans ut.Translator

// InitTrans 初始化翻译器
func InitTrans(locale string) (err error) {
	// 修改gin框架中的Validator引擎属性，实现自定制
	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {

		// 注册一个获取json tag的自定义方法
		v.RegisterTagNameFunc(func(fld reflect.StructField) string {
			name := strings.SplitN(fld.Tag.Get("json"), ",", 2)[0]
			if name == "-" {
				return ""
			}
			return name
		})

		zhT := zh.New() // 中文翻译器
		enT := en.New() // 英文翻译器

		// 第一个参数是备用（fallback）的语言环境
		// 后面的参数是应该支持的语言环境（支持多个）
		// uni := ut.New(zhT, zhT) 也是可以的
		uni := ut.New(enT, zhT, enT)

		// locale 通常取决于 http 请求头的 'Accept-Language'
		var ok bool
		// 也可以使用 uni.FindTranslator(...) 传入多个locale进行查找
		trans, ok = uni.GetTranslator(locale)
		if !ok {
			return fmt.Errorf("uni.GetTranslator(%s) failed", locale)
		}

		// 注册翻译器
		switch locale {
		case "en":
			err = enTranslations.RegisterDefaultTranslations(v, trans)
		case "zh":
			err = zhTranslations.RegisterDefaultTranslations(v, trans)
		default:
			err = enTranslations.RegisterDefaultTranslations(v, trans)
		}
		return
	}
	return
}

// removeTopStruct 去除提示信息中的结构体名称
func removeTopStruct(fields map[string]string) map[string]string {
	res := map[string]string{}
	for field, err := range fields {
		res[field[strings.Index(field, ".")+1:]] = err
	}
	return res
}

func main() {
	//初始化翻译器，翻译英文的err
	if err := InitTrans("zh"); err != nil {
		fmt.Println("init trans err:", err)
		return
	}

	r := gin.Default()
	r.POST("/login", func(c *gin.Context) {
		p := new(ParamSignUp)
		if err := c.ShouldBindJSON(p); err != nil {
			//请求参数有误，直接返回响应
			zap.L().Error("SignUp with invalid param", zap.Error(err))
			//判断err是不是validator.ValidationErrors类型
			errs, ok := err.(validator.ValidationErrors)
			if !ok {
				c.JSON(200, err.Error())
			}
			//如果是就要翻译这个错误
			c.JSON(200, removeTopStruct(errs.Translate(trans)))
			/*c.JSON(http.StatusOK, gin.H{
				"msg": removeTopStruct(errs.Translate(trans)),
			})*/
			return
		}
		c.JSON(200, "登录成功")
	})
	r.Run()
}

```



## 6.md5加密

导入"crypto/md5"包

```go
const secret = "5134dfad.com"
// encryptPassword 对密码进行加密
func encryptPassword(oPassword string) string {
	h := md5.New()
	h.Write([]byte(secret))
	return hex.EncodeToString(h.Sum([]byte(oPassword)))
}
```



# 文件分层

`mvc模式：`

**controller（控制器）：**控制整个数据的流向,数据变化的时候更新视图

**model（模型）：**程序员编写应用程序应该具有的功能实现一些算法，与代码作成映射的关系

**view(视图)：**图形化简便的一些设计

`cld模型`

**controller（控制器）:** 整个Web服务的一个入口，负责处理路由，参数的一些校验，请求的转发等等，然后继续传递给logic这一层

**longic（逻辑层）:** 真正处理业务逻辑的地方（调用DAO层的函数或者接口）

**DAO:**（reponsitory）:下一层的存储的内容，Mysql，redis等交互的操作对外分装成简单的函数

## 1.controller

控制层，定义每个注册的路由的HandlerFunc函数func(*Context)，每个HandlerFunc函数分别**对每个请求进行处理**。

进行**获取参数并进行校验、请求转发**等等操作。之后**传递给logic逻辑层进行处理**。**最后返回响应**。

### 定义响应码

在code.go中定义各种响应码以及对应的描述。用map[int64]string存

```go
package controller

type ResCode int64

const (
	CodeSuccess ResCode = 100 + iota
	CodeInvalidParam
	CodeUserExist
	CodeUserNotExist
	CodeInvalidPassword
	CodeServerBusy
	CodeInvalidToken
	CodeNeedLogin
	CodeVoteTimeExpire
)

var codeMsgMap = map[ResCode]string{
	CodeSuccess:         "success",
	CodeInvalidParam:    "请求参数错误",
	CodeUserExist:       "用户名已存在",
	CodeUserNotExist:    "用户名不存在",
	CodeInvalidPassword: "用户名或密码错误",
	CodeServerBusy:      "服务繁忙",
	CodeInvalidToken:    "无效的token",
	CodeNeedLogin:       "需要登录",
	CodeVoteTimeExpire:  "投票时间已过",
}

func (c ResCode) Msg() string {
	msg, ok := codeMsgMap[c]
	if !ok {
		msg = codeMsgMap[CodeServerBusy]
	}
	return msg
}
```

### 定义响应数据结构体

在response.go文件中**定义ResponseData结构体**，包含三个属性：**响应码、响应码对应描述信息、响应数据**

```go
package controller

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

type ResponseData struct {
	Code ResCode     `json:"code,omitempty"`
	Msg  interface{} `json:"msg,omitempty"`
	Data interface{} `json:"data,omitempty"`
}
```

### 返回响应数据

在response.go文件中定义响应方法，根据不同的响应形式，调用不同的响应方法。

有些请求不需要响应数据，例如**登录、注册**等等，而有些要，例如**查询**等等

```go
func ResponseError(c *gin.Context, code ResCode) {
	var rd = ResponseData{
		Code: code,
		Msg:  code.Msg(),
		Data: nil,
	}
	c.JSON(http.StatusOK, rd)
}

func ResponseErrorWithMsg(c *gin.Context, code ResCode, msg interface{}) {
	var rd = ResponseData{
		Code: code,
		Msg:  msg,
		Data: nil,
	}
	c.JSON(http.StatusOK, rd)
}

func ResponseSuccess(c *gin.Context, data interface{}) {
	var rd = ResponseData{
		Code: CodeSuccess,
		Msg:  CodeSuccess.Msg(),
		Data: data,
	}
	c.JSON(http.StatusOK, rd)
}
```

## 2.logic

逻辑层，**传入参数后对每个请求进行具体的逻辑处理**，**之后对数据层进行操作**，返回dao层`可能产生的错误或数据`。例如，通过用户名或密码进行注册，先要判断用户是否已经存在。

对每个功能分文件编写。例如社区功能、用户功能、帖子功能、投票功能

![logic层次](D:\E\学习\Go\笔记\asset\logic层次.png)

## 3.dao

一个请求最终要进行的层次，对数据进行操作，可能也包含一定的逻辑处理。主要操作mysql，有些业务功能要用到缓存，就使用redis。返回`可能产生的错误或数据`给logic层

![image-20240420194024588](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240420194024588.png)

**在errors.go中定义相关的error，在redis中定义一些key值**

```go
package mysql

import "errors"

var (
	ErrorUserExist       = errors.New("用户已存在")
	ErrorUserNotExist    = errors.New("用户不存在")
	ErrorInvalidPassword = errors.New("用户名或密码错误")
	ErrorInvalidID       = errors.New("无效的id")
	ErrorQueryFailed     = errors.New("数据库查询错误")
)
```

### mysql初始化

mysql使用的是sqlx库，便于查询操作，也可以使用更强大的gorm框架

mysql的初始化如下，传入的配置信息有：user、password、host、port、db

```go
package mysql

import (
	"bluebell/pkg/settings"
	"fmt"

	"go.uber.org/zap"

	_ "github.com/go-sql-driver/mysql"
	"github.com/jmoiron/sqlx"
)

var db *sqlx.DB

func Init(cfg *settings.MySQLConfig) (err error) {
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		cfg.User, cfg.Password, cfg.Host, cfg.Port, cfg.DB)
	// 也可以使用MustConnect连接不成功就panic
	db, err = sqlx.Connect("mysql", dsn)
	if err != nil {
		zap.L().Error("connect DB failed", zap.Error(err))
		return
	}
	db.SetMaxOpenConns(cfg.MaxOpenConns)
	db.SetMaxIdleConns(cfg.MaxIdleConns)
	return
}

// Close 关闭db
func Close() {
	db.Close()
}
```



### redis初始化

redis使用的是go-redis库

初始化传入的配置信息有：host、port、pool_size等等

```go
package redis

import (
	"bluebell/pkg/settings"
	"fmt"

	"github.com/go-redis/redis"
)

var rdb *redis.Client

func Init(cfg *settings.RedisConfig) (err error) {
	rdb = redis.NewClient(&redis.Options{
		Addr:         fmt.Sprintf("%s:%d", cfg.Host, cfg.Port),
		Password:     cfg.Password, // no password set
		DB:           cfg.DB,       // use default DB
		PoolSize:     cfg.PoolSize,
		MinIdleConns: cfg.MinIdleConns,
	})
	_, err = rdb.Ping().Result()
	return
}

func Close() {
	rdb.Close()
}
```

## 4.models

定义对象的结构体，不限于数据库中的表对象，还有比如登录或注册参数，投票数据等。

![image-20240420193940671](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240420193940671.png)

## 5.router

进行**路由注册**，还有**中间件的使用**，例如jwt中间件。

```go
package router

import (
	"bluebell/controller"
	_ "bluebell/pkg/docs" // 千万不要忘了导入把你上一步生成的docs
	"bluebell/pkg/logger"
	"bluebell/pkg/middlewares"
	"net/http"

	swaggerFiles "github.com/swaggo/files"
	gs "github.com/swaggo/gin-swagger"

	"github.com/gin-gonic/gin"
)

func SetUpRouter(mode string) *gin.Engine {
	//将gin设置成发布模式不在终端输出信息
	if mode == gin.ReleaseMode {
		gin.SetMode(gin.ReleaseMode)
	}
	r := gin.New()
	r.Use(logger.GinLogger(), logger.GinRecovery(true))

	//注册业务路由
	r.GET("/swagger/*any", gs.WrapHandler(swaggerFiles.Handler))

	v1 := r.Group("/api/v1")

	v1.POST("/signup", controller.SignUpHandler)
	v1.POST("/login", controller.LoginHandler)

	//使用jwt认证中间件
	v1.Use(middlewares.JWTAuthMiddleware())

	{
		v1.GET("/community", controller.CommunityHandler)
		v1.GET("/community/:id", controller.CommunityDetailHandler)

		v1.POST("/post", controller.CreatePostHandler)
		v1.POST("/post/:id", controller.GetPostDetailHandler)
		v1.GET("/posts", controller.GetPostListHandler)
		//根据时间或分数获取帖子列表
		v1.POST("/posts2", controller.GetPostListHandler2)
		//投票
		v1.POST("/vote", controller.PostVoteController)
	}
	r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"msg": "404",
		})
	})
	return r
}

```



## 6.pkg

存用到的各种外部库，算法，中间件

![image-20240420195227115](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240420195227115.png)

# 功能实现

## 用户功能

### 1.用户注册

```go
// ParamSignUp 注册参数
type ParamSignUp struct {
	Username   string `json:"username" binding:"required" form:"username"`
	Password   string `json:"password" binding:"required" form:"password"`
	RePassword string `json:"re_password" binding:"required,eqfield=Password" form:"re_password"`
}
```

#### controller层

输入**用户名，密码，重复密码**。绑定注册参数结构体获取这三个参数，使用validator参数校验，**username、password、re_password都不能为空，re_password必须等于password**。如果有错误直接返回给前端。

参数无误，成功获取到后，交给logic层处理。

```go
// SignUpHandler 处理注册请求的函数
func SignUpHandler(c *gin.Context) {
	//1.获取参数和validator参数校验
	p := new(models.ParamSignUp)
	if err := c.ShouldBindJSON(p); err != nil {
		//请求参数有误，直接返回响应
		zap.L().Error("SignUp with invalid param", zap.Error(err))
		//判断err是不是validator.ValidationErrors类型
		errs, ok := err.(validator.ValidationErrors)
		if !ok {
			ResponseError(c, CodeInvalidParam)
		}
		//如果是就要翻译这个错误
		ResponseErrorWithMsg(c, CodeInvalidParam, removeTopStruct(errs.Translate(trans)))
		/*c.JSON(http.StatusOK, gin.H{
			"msg": removeTopStruct(errs.Translate(trans)),
		})*/
		return
	}
	//fmt.Println(p)
	//2.业务处理
	if err := logic.SignUp(p); err != nil {
		zap.L().Error("logic.SignUp err", zap.Error(err))
		//判断是不是用户不存在
		if err == mysql.ErrorUserExist {
			ResponseError(c, CodeUserExist)
		} else {
			ResponseError(c, CodeServerBusy)
		}
		return
	}
	ResponseSuccess(c, nil)
}
```

#### logic层

首先在数据库中查询当前用户是否存在，如果存在就注册失败返回err。

如果不存在，就创建一个user**使用雪花算法生成id**，添加到数据库。

```go
// SignUp 注册逻辑
func SignUp(p *models.ParamSignUp) (err error) {
	//判断用户存不存在
	if err = mysql.CheckUserExist(p.Username); err != nil {
		//数据库查询出错
		return
	}
	//生成UID，创建user
	userID := snowflake.GenID()
	user := &models.User{
		UserID:   userID,
		Username: p.Username,
		Password: p.Password,
	}
	//保存进数据库
	return mysql.AddUser(user)
}
```

#### dao层

sqlStr表示查找用户名为username的用户是否存在，存在count>0。也可以用count(1)，

```go
// CheckUserExist 检查指定用户名的用户是否存在
func CheckUserExist(username string) (err error) {
	sqlStr := "select count(user_id) from user where username = ?"
	var count int
	if err = db.Get(&count, sqlStr, username); err != nil {
		return err
	}
	if count > 0 {
		return ErrorUserExist
	}
	return
}
```

首先对user的密码进行md5加密，再将用户存进数据库user表

```go
// AddUser 向数据库中插入一个用户数据
func AddUser(user *models.User) (err error) {
	//对用户的密码进行加密
	user.Password = encryptPassword(user.Password)
	sqlStr := "insert into user (user_id, username, password) values (?, ?, ?)"
	_, err = db.Exec(sqlStr, user.UserID, user.Username, user.Password)
	return
}
```

### 2.用户登录

```go
type User struct {
	UserID   int64  `db:"user_id,string" json:"user_id,string"`
	Username string `db:"username" json:"username"`
	Password string `db:"password" json:"password"`
	Token    string `json:"token,omitempty"`
}
```

#### contoller层

登录参数和注册参数是一样的，获取到这三个参数后就交给逻辑层处理。

如果登录失败返回**用户不存在**或**密码错误**

登录成功后就给前端返回**用户id，用户名以及生成的token**

**前端因为不能存64位的id故使用字符串表示，可以在user结构体中json的tag标注string。注意给前端返回id要转换为字符串。**

```go
// LoginHandler 处理登录请求
func LoginHandler(c *gin.Context) {
	//1.获取并校验参数
	p := new(models.ParamLogin)
	if err := c.ShouldBind(p); err != nil {
		zap.L().Error("login with invalid param", zap.Error(err))
		//判断err是不是validator.ValidationErrors类型
		errs, ok := err.(validator.ValidationErrors)
		if !ok {
			ResponseError(c, CodeInvalidParam)
		}
		//如果是就要翻译这个错误
		ResponseErrorWithMsg(c, CodeInvalidParam, removeTopStruct(errs.Translate(trans)))
		return
	}
	//2.逻辑处理
	user, err := logic.Login(p)
	if err != nil {
		zap.L().Error("logic.Login err", zap.Error(err))
		if err == mysql.ErrorUserNotExist {
			ResponseError(c, CodeUserNotExist)
		}
		ResponseError(c, CodeInvalidPassword)
		return
	}
	ResponseSuccess(c, gin.H{
		"user_id":   fmt.Sprint(user.UserID), //id大于1<<53-1时，在前端数据会失真
		"user_name": user.Username,
		"token":     user.Token,
	})
}
```

#### logic层

根据传进来的username和password创建user，登录成功后根据userId和username生成一个token。

```go
// Login 登录逻辑
func Login(p *models.ParamLogin) (user *models.User, err error) {
	user = &models.User{
		Username: p.Username,
		Password: p.Password,
	}
	if err = mysql.Login(user); err != nil {
		return
	}
	//fmt.Println(user)
	//登录成功，生成JWT
	user.Token, err = jwt.GenToken(user.UserID, user.Username)
	return
}
```

#### dao层

先对user的password进行MD5加密得到加密文本。然后根据username查找用户是否存在，查找到user后，将加密后的密码与数据库中已经加密的密码进行判断，如果相等就登录成功，否则密码错误。

```go
// Login 查找登录的用户名和密码是否存在
func Login(user *models.User) (err error) {
	oPassword := user.Password
	//查询用户是否存在
	sqlStr := "select user_id, username, password from user where username = ?"
	err = db.Get(user, sqlStr, user.Username)
	if err != nil { //这个错误不一定是用户不存在
		if err == sql.ErrNoRows {
			return ErrorUserNotExist
		}
		return
	}
	//判断密码是否正确
	password := encryptPassword(oPassword)
	if password != user.Password {
		return ErrorInvalidPassword
	}
	return
}
```

## 社区功能

### 1.查询所有社区

```go
type Community struct {
	ID   int64  `json:"id" db:"community_id"`
	Name string `json:"name" db:"community_name"`
	//Introduction string `json:"introduction,omitempty" db:"introduction"`
	//CreateTime   time.Time `json:"create_time" db:"create_time"`
}
```

#### contoller层

```go
// CommunityHandler 查询所有社区
func CommunityHandler(c *gin.Context) {
	//查询到所有的社区（community_id, community_name) 以列表的形式返回
	data, err := logic.GetCommunityList()
	if err != nil {
		zap.L().Error("logic.GetCommunityList() err", zap.Error(err))
		ResponseError(c, CodeServerBusy) //不轻易将服务端报错暴露给外面
		return
	}
	ResponseSuccess(c, data)
}
```

#### logic层

```go
func GetCommunityList() ([]models.Community, error) {
	//查找数据库中所有的community，并返回
	return mysql.GetCommunityList()
}
```

#### dao层

```go
func GetCommunityList() (communityList []models.Community, err error) {
	sqlStr := "select community_id, community_name from community"
	if err := db.Select(&communityList, sqlStr); err != nil {
		if err == sql.ErrNoRows {
			zap.L().Warn("there is no community in db")
			err = nil
		}
	}
	return
}
```

### 2.根据id查询社区详情

```go
type CommunityDetail struct {
   ID           int64     `json:"id" db:"community_id"`
   Name         string    `json:"name" db:"community_name"`
   Introduction string    `json:"introduction,omitempty" db:"introduction"`
   CreateTime   time.Time `json:"create_time" db:"create_time"`
}
```

#### contoller层

首先获取path参数中的社区id，然后查询。

```go
// CommunityDetailHandler 社区分类详情
func CommunityDetailHandler(c *gin.Context) {
	//获取社区id参数(url)
	idStr := c.Param("id")
	id, err := strconv.ParseInt(idStr, 10, 64)
	if err != nil {
		ResponseError(c, CodeInvalidParam)
		return
	}
	//查询社区（community_id, community_name)
	data, err := logic.GetCommunityDetail(id)
	if err != nil {
		zap.L().Error("logic.GetCommunityList() err", zap.Error(err))
		ResponseError(c, CodeServerBusy) //不轻易将服务端报错暴露给外面
		return
	}
	ResponseSuccess(c, data)
}
```

#### logic层

```go
// GetCommunityDetail 根据id查询社区详情
func GetCommunityDetail(id int64) (*models.CommunityDetail, error) {
	//查找数据库中所有的community，并返回
	return mysql.GetCommunityDetailByID(id)
}
```

#### dao层

```go
func GetCommunityDetailByID(id int64) (communityDetail *models.CommunityDetail, err error) {
	communityDetail = new(models.CommunityDetail)
	sqlStr := "select community_id, community_name, introduction, create_time from community where community_id = ?"
	if err = db.Get(communityDetail, sqlStr, id); err != nil {
		if err == sql.ErrNoRows {
			err = ErrorInvalidID
		}
	}
	return
}
```

## 帖子功能

### 1.创建帖子

前端以JSON的形式传递参数，必须有**title和content以及communityID**。controller层接收到参数后，先**获取当前用户id**，一起封装层Post对象，传入logic层。

logic层通过**雪花算法生层PostID**，将Post存入数据库，并将当前贴子存入redis的zset

dao层，因为后续有贴子排序，所以将新创建的postid按创建时间和投票数存入两个有序集合zset中。

```go
type Post struct {
	PostID      int64     `json:"post_id,string" db:"post_id"`
	AuthorId    int64     `json:"author_id,string" db:"author_id"`
	CommunityID int64     `json:"community_id,string" db:"community_id" binding:"required"`
	Status      int32     `json:"status" db:"status"`
	Title       string    `json:"title" db:"title" binding:"required"`
	Content     string    `json:"content" db:"content" binding:"required"`
	CreateTime  time.Time `json:"-" db:"create_time"`
}
```

#### controller层

```go
// CreatePostHandler 创建一个贴子
func CreatePostHandler(c *gin.Context) {
	//1.获取参数及参数的校验
	p := new(models.Post)
	if err := c.ShouldBindJSON(p); err != nil {
		zap.L().Error("create post with invalid param", zap.Error(err))
		ResponseError(c, CodeInvalidParam)
		return
	}
	//从c中取出发送帖子的登录用户id
	authorID, err := GetCurrentUserID(c)
	if err != nil {
		ResponseError(c, CodeNeedLogin)
		return
	}
	p.AuthorId = authorID
	//2.创建帖子
	if err := logic.CreatePost(p); err != nil {
		zap.L().Error("logic.CreatePost(p) err", zap.Error(err))
		ResponseError(c, CodeServerBusy)
		return
	}
	//3.返回
	ResponseSuccess(c, CodeSuccess)
}
```

#### logic层

```go
// CreatePost 创建一个帖子
func CreatePost(p *models.Post) (err error) {
	//1.生成post_id
	p.PostID = snowflake.GenID()
	//2.保存到数据库
	err = mysql.CreatePost(p)
	if err != nil {
		return
	}
	err = redis.CreatePost(p.PostID)
	return
}
```

#### dao层

```go
// CreatePost 创建一个帖子
func CreatePost(p *models.Post) (err error) {
	sqlStr := `insert into post (post_id, title, content, author_id, community_id)
		values (?, ?, ?, ?, ?)`
	_, err = db.Exec(sqlStr, p.PostID, p.Title, p.Content, p.AuthorId, p.CommunityID)
	return
}
```

```go
// CreatePost 将帖子的创建时间和帖子的初始分数存入redis
func CreatePost(postID int64) (err error) {
	//go-redis事务操作
	pipeline := rdb.TxPipeline()
	//帖子发布时间
	pipeline.ZAdd(getRedisKey(KeyPostTimeZSet), redis.Z{
		Score:  float64(time.Now().Unix()),
		Member: postID,
	})
	//帖子分数
	pipeline.ZAdd(getRedisKey(KeyPostScoreZSet), redis.Z{
		Score:  0,
		Member: postID,
	})
	_, err = pipeline.Exec()
	return
}
```



### 2.根据id获取帖子详情

获取路径参数转换成postId

根据postId查找post，根据userId查找user，根据communityId查找community。获取到userName和communityName拼接到帖子详情ApiPostDetail中。

```go
type ApiPostDetail struct {
	*Post
	AuthorName    string `json:"author_name"`
	CommunityName string `json:"community_name"`
	VoteNum       int64  `json:"vote_num"`
}
```

#### controller层

```go
// GetPostDetailHandler 根据id获取帖子详情
func GetPostDetailHandler(c *gin.Context) {
	//1.获取并校验参数
	idStr := c.Param("id")
	id, err := strconv.ParseInt(idStr, 10, 64)
	if err != nil {
		zap.L().Error("get post detail with invalid param", zap.Error(err))
		ResponseError(c, CodeInvalidParam)
		return
	}
	//2.查找帖子详情
	data, err := logic.GetPostDetail(id)
	if err != nil {
		zap.L().Debug("", zap.Error(err))
		ResponseError(c, CodeServerBusy)
		return
	}
	//3.返回
	ResponseSuccess(c, data)
}
```

#### logic层

```go
// GetPostDetail 获取帖子详情
func GetPostDetail(id int64) (post *models.ApiPostDetail, err error) {
	//查询并组合成我们想要的帖子详情信息
	post, err = mysql.GetPostByID(id)
	if err != nil {
		zap.L().Error("mysql.GetPostByID err", zap.Error(err))
		return
	}
	//根据作者id查询作者信息
	user, err := mysql.GetUserByID(post.AuthorId)
	if err != nil {
		zap.L().Error("mysql.GetUserById err", zap.Error(err))
		return
	}
	//根据社区id查询社区信息
	communityDetail, err := mysql.GetCommunityDetailByID(post.CommunityID)
	if err != nil {
		zap.L().Error("mysql.GetCommunityDetailByID err", zap.Error(err))
		return
	}
	post.AuthorName = user.Username
	post.CommunityName = communityDetail.Name
	return
}
```

#### dao层

```go
// GetPostByID 根据id查询单个帖子
func GetPostByID(id int64) (post *models.ApiPostDetail, err error) {
	post = new(models.ApiPostDetail)
	sqlStr := `select post_id, title, content, author_id, community_id, create_time
	from post
	where post_id = ?`
	err = db.Get(post, sqlStr, id)
	if err == sql.ErrNoRows {
		err = ErrorInvalidID
		return
	}
	if err != nil {
		zap.L().Error("query post failed", zap.String("sql", sqlStr), zap.Error(err))
		err = ErrorQueryFailed
		return
	}
	return
}
```

### 3.分页获取所有帖子详情列表

前端参数以QueryString的方式传递。controller层接收两个参数，**页码，页大小**。

logic层首先根据页码和页大小获取该页的所有贴子列表，同样的对于每个帖子还要去查找AuthorName和communityName。

dao层`select * from post limit a, b`表示从第a+1条记录开始，取b条。`db.Select(&posts, sqlStr, (page-1)*size, size)`就可以实现查找第page页的所有数据。

#### controller层

```go
// GetPageInfo 获取分页参数，多少页page以及每页大小size
func GetPageInfo(c *gin.Context) (int64, int64) {
	pageStr := c.Query("page")
	SizeStr := c.Query("size")
	var (
		page int64
		size int64
		err  error
	)
	page, err = strconv.ParseInt(pageStr, 10, 64)
	if err != nil {
		page = 1
	}
	size, err = strconv.ParseInt(SizeStr, 10, 64)
	if err != nil {
		size = 10
	}
	return page, size
}

// GetPostListHandler 获取帖子列表的处理函数
func GetPostListHandler(c *gin.Context) {
	//获取分页参数
	page, size := GetPageInfo(c)
	//查找对应帖子列表
	data, err := logic.GetPostList(page, size)
	if err != nil {
		zap.L().Error("logic.GetPostList() err", zap.Error(err))
		return
	}
	ResponseSuccess(c, data)
}
```

#### logic层

```go
// GetPostList 分页获取帖子详情列表
func GetPostList(page, size int64) (data []models.ApiPostDetail, err error) {
	postList, err := mysql.GetPostList(page, size)
	if err != nil {
		fmt.Println(err)
		return
	}
	data = make([]models.ApiPostDetail, 0, len(postList))
	for _, post := range postList {
		user, err := mysql.GetUserByID(post.AuthorId)
		if err != nil {
			zap.L().Error("mysql.GetUserByID() failed", zap.Any("author_id", post.AuthorId), zap.Error(err))
			continue
		}
		post.AuthorName = user.Username
		community, err := mysql.GetCommunityDetailByID(post.CommunityID)
		if err != nil {
			zap.L().Error("mysql.GetCommunityByID() failed", zap.String("community_id", fmt.Sprint(post.CommunityID)), zap.Error(err))
			continue
		}
		post.CommunityName = community.Name
		data = append(data, post)
	}
	return
}
```

#### dao层

```go
// GetPostList 查询所有帖子
func GetPostList(page, size int64) (posts []models.ApiPostDetail, err error) {
	sqlStr := `select post_id, title, content, author_id, community_id, create_time
	from post
	ORDER BY create_time desc
	limit ?, ?
	`
	posts = make([]models.ApiPostDetail, 0, 2)
	err = db.Select(&posts, sqlStr, (page-1)*size, size)
	return
}
```

### 4.根据时间或分数或社区id分页获取帖子列表

如果communityId=0，就获取所有帖子详情，否则获取对应社区的贴子。同样也是分页查询

```go
// ParamPostList 帖子列表queryString参数
type ParamPostList struct {
	CommunityID int64  `json:"community_id" from:"community_id"` //可以为空
	Page        int64  `json:"page" form:"page"`
	Size        int64  `json:"size" form:"size"`
	Order       string `json:"order" form:"order"`
}
```

#### controller层

```go
// GetPostListHandler2 根据前端传来参数动态获取帖子列表
// 按时间 按分数

// GetPostListHandler2 升级版帖子列表接口
// @Summary 升级版帖子列表接口
// @Description 可按社区按时间或分数排序查询帖子列表接口
// @Tags 帖子相关接口
// @Accept application/json
// @Produce application/json
// @Param Authorization header string false "Bearer 用户令牌"
// @Param object query models.ParamPostList false "查询参数"
// @Security ApiKeyAuth
// @Success 200 {object} _ResponsePostList
// @Router /posts2 [get]
func GetPostListHandler2(c *gin.Context) {
	//获取分页参数
	//GET请求参数 /api/v1/posts2?page=1&size=10&order=time
	p := &models.ParamPostList{
		Page:  1,
		Size:  10,
		Order: models.OrderTime,
	}
	if err := c.ShouldBind(p); err != nil {
		zap.L().Error("GetPostListHandler2 with invalid param", zap.Error(err))
		ResponseError(c, CodeInvalidParam)
		return
	}
	//查找对应帖子列表
	var data []models.ApiPostDetail
	var err error
	if p.CommunityID == 0 {
		data, err = logic.GetPostList2(p)
	} else {
		data, err = logic.GetCommunityPostList(p)
	}
	if err != nil {
		zap.L().Error("logic.GetPostList() err", zap.Error(err))
		return
	}
	ResponseSuccess(c, data)
}
```

#### logic层

首先查询所有根据排序方法，从redis中查询有序ids

根据要不要使用commnityID查询帖子，使用两个不同的sql语句。

查询到所有帖子后，在此为每个帖子查询Authorname和commnityName.

```go
// GetPostList2 根据前端传来参数动态获取帖子列表（order, community_id）
func GetPostList2(p *models.ParamPostList) (data []models.ApiPostDetail, err error) {
	//从数据库中查询id的有序列表
	ids, err := redis.GetPostInOrder(p)
	if err != nil {
		zap.L().Error("redis.GetPostInOrder(p) err", zap.Error(err))
		return
	}
	if len(ids) == 0 {
		zap.L().Warn("redis.GetPostIDsInOrder return 0 data")
		return
	}
	//根据id去mysql数据库中查询帖子列表
	//返回的数据还要按照给定的顺序返回
    var postList []models.ApiPostDetail
    if p.CommunityId == 0 {
        postList, err = mysql.GetPostListByIDs(ids)
    } else {
        postList, err = mysql.GetCommunityPostListByIDs(ids, p.CommunityID)
    }
	if err != nil {
		return
	}
	//提前查询好每篇帖子的投票数
	voteNums, err := redis.GetPostVoteData(ids)
	if err != nil {
		return
	}
	for i, post := range postList {
		user, err := mysql.GetUserByID(post.AuthorId)
		if err != nil {
			zap.L().Error("mysql.GetUserByID() failed", zap.Any("author_id", post.AuthorId), zap.Error(err))
			continue
		}
		community, err := mysql.GetCommunityDetailByID(post.CommunityID)
		if err != nil {
			zap.L().Error("mysql.GetCommunityByID() failed", zap.String("community_id", fmt.Sprint(post.CommunityID)), zap.Error(err))
			continue
		}
		post.AuthorName = user.Username
		post.CommunityName = community.Name
		post.VoteNum = voteNums[i]
		data = append(data, post)
	}
	return
}
```

#### dao层

分页从redis中查询有序集合。

```go
// GetPostInOrder 按order参数从redis中获取帖子的id有序集合
func GetPostInOrder(p *models.ParamPostList) ([]string, error) {
	//从redis获取所有帖子id
	key := getRedisKey(KeyPostTimeZSet)
	if p.Order == models.OrderScore {
		key = getRedisKey(KeyPostScoreZSet)
	}
	//确定索引范围
	start := (p.Page - 1) * p.Size
	end := start + p.Size - 1
	//按分数从大到小查询(时间或票数)
	return rdb.ZRevRange(key, start, end).Result()
}
```

`order by FIND_IN_SET(post_id, ?)`表示根据post_id在ids中的顺序进行排序。

```go
// GetPostListByIDs 根据id列表按序查询帖子列表
func GetPostListByIDs(ids []string) (postList []models.ApiPostDetail, err error) {
   sqlStr := `select post_id, title, content, author_id, community_id, create_time
   from post
   where post_id in (?) 
   order by FIND_IN_SET(post_id, ?)`
   query, args, err := sqlx.In(sqlStr, ids, strings.Join(ids, ","))
   if err != nil {
      zap.L().Error("sqlx.In err", zap.Error(err))
      return
   }
   query = db.Rebind(query)
   err = db.Select(&postList, query, args...)
   if err != nil {
      zap.L().Error("db.Select err", zap.Error(err))
      return
   }
   return
}

// GetCommunityPostListByIDs 根据id列表和社区id按序查询帖子列表
func GetCommunityPostListByIDs(ids []string, communityId int64) (postList []models.ApiPostDetail, err error) {
   sqlStr := `select post_id, title, content, author_id, community_id, create_time
   from post
   where post_id in (?) and community_id = ?
   order by FIND_IN_SET(post_id, ?)`
   query, args, err := sqlx.In(sqlStr, ids, communityId, strings.Join(ids, ","))
   if err != nil {
      zap.L().Error("sqlx.In err", zap.Error(err))
      return
   }
   query = db.Rebind(query)
   err = db.Select(&postList, query, args...)
   if err != nil {
      zap.L().Error("db.Select err", zap.Error(err))
      return
   }
   return
}
```

## 投票

```go
// VoteData 投票数据
type VoteData struct {
	//userid从请求中获取
	PostID    int64 `json:"post_id,string"`
	Direction int   `json:"direction,string"` //赞成票(1)还是反对票(-1)
}
```

#### controller层

```go
func PostVoteController(c *gin.Context) {
	//获取并校验参数
	p := new(models.ParamVoteData)
	if err := c.ShouldBindJSON(p); err != nil {
		errs, ok := err.(validator.ValidationErrors) //类型断言
		zap.L().Error("c.shouldBindJSON err", zap.Error(err))
		if !ok {
			ResponseError(c, CodeInvalidParam)
		} else {
			errData := removeTopStruct(errs.Translate(trans))
			ResponseErrorWithMsg(c, CodeInvalidParam, errData)
		}
		return
	}
	//获取user_id
	userID, err := GetCurrentUserID(c)
	if err != nil {
		ResponseError(c, CodeNeedLogin)
	}

	if err := logic.VoteForPost(userID, p); err != nil {
		zap.L().Error("logic.VoteForPost err", zap.Error(err))
		ResponseError(c, CodeVoteTimeExpire)
		return
	}
	ResponseSuccess(c, nil)
}
```

#### logic层

```go
func VoteForPost(userID int64, p *models.ParamVoteData) (err error) {
	return redis.VoteForPost(strconv.Itoa(int(userID)), strconv.Itoa(int(p.PostID)), float64(p.Direction))
}
```

#### dao层

有三个参数userID、postId、v，v为-1,0,1分别表示投反对票、投票取消、投赞成票。

```go
// VoteForPost 投票功能：
/*
	投一票就加432分    86400/200
	direction=1 0 -1
		direction=1时，有两种情况
			1.之前没投过票，现在要投赞成票	+432
			2.之前投过反对票，现在要改为赞成票 +432*2
		direction=0时，有两种情况
			1.之前投过赞成票，现在要取消 	+432
			2.之前投过反对票，现在要取消		-432
		direction=-1时，有两种情况
			1.之前没投过票，现在要投反对票	 
			2.之前投过赞成票，现在要改为反对票 -432*2

	投票的限制：每个帖子自发表之日一个星期之内允许晕乎投票，超过一个星期就不允许投票了
		1.到期之后redis中保存的赞成票数及反对票数存到mysql表中
		2.到期之后删除那个KeyPostVotedZSetPF
*/

func VoteForPost(userID, postID string, v float64) (err error) {
	//1.判断投票限制
	//获取帖子发布的时间
	postTime := rdb.ZScore(getRedisKey(KeyPostTimeZSet), postID).Val()
	if float64(time.Now().Unix())-postTime > onWeekInSeconds {
		//不允许投票了
		return ErrVoteTimeExpire
	}
	//2.更新分数
	//先查当前用户给当前帖子的投票记录
	ov := rdb.ZScore(getRedisKey(KeyPostVotedZSetPF+postID), userID).Val()
	//如果这一个投票和之前保存的值一致，就提示不允许重复投票
	if v == ov {
		return ErrVoteRepeated
	}
	var dir float64
	if v > ov {
		dir = 1
	} else {
		dir = -1
	}
	//计算两次投票的差值，结果为（2，1）
	pipeline := rdb.TxPipeline()
	diff := math.Abs(ov - v)
	pipeline.ZIncrBy(getRedisKey(KeyPostScoreZSet), dir*diff*scorePerVote, postID)
	//3.记录用户为该帖子投票的数据
	if v == 0 { //删除投票记录
		pipeline.ZRem(getRedisKey(KeyPostVotedZSetPF + postID)， userID)
	} else {
		pipeline.ZAdd(getRedisKey(KeyPostVotedZSetPF+postID), redis.Z{
			Score:  v,
			Member: userID,
		})
	}
	_, err = pipeline.Exec()
	return
}
```



### 3.查询所有社区

```go

```

#### controller层

```go

```

#### logic层

```go

```

#### dao层

```go

```





