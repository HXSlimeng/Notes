# Gin框架

- `r.RunTLS()`可以启动一个`https`服务需要传入证书

- `r.Static()` 是一个用于处理静态文件的函数或方法。它的作用是将指定的目录中的静态文件（如HTML、CSS、JavaScript、图像等）映射到Web应用程序的URL路径上，以便可以通过浏览器访问这些静态文件。

  ```go
  //访问/static/index.html 会返回服务器地址/home/index.html
  	r := gin.Default()
  	r.Static("/static","./home")
  	r.Run()
  ```


### 数据方法绑定校验

```go
type LoginForm struct{
	Username string `form:"user" binding:"required"`
	Password string `form:"password" binding:"required"`
}

func main() {
	r := gin.Default()

	r.Static("/static","./home")
	r.POST("/login",func (c *gin.Context)  {
		var form LoginForm
        //ShouldBind该方法会自动根据请求头获取对应的方法，并使用form的类型对req数据进行校验
        //需要指定请求方法的话可以使用 shouldBind
		err := c.ShouldBind(&form)
		if err == nil {
			if form.Username == "user" && form.Password == "password" {
				c.JSON(200, gin.H{"status": "you are logged in"})
			} else {
				c.JSON(401, gin.H{"status": "unauthorized"})
			}
		}
	})
	r.Run()
	
}
```

### 表单

```go
router.POST("/form_post", func(c *gin.Context) {
    //postForm(key) 会尝试获取key字段的值 否则返回空字符串
		message := c.PostForm("message")
    //DefaultPostForm("key",value) 会尝试获取key字段的值 否则返回value值
		nick := c.DefaultPostForm("nick", "anonymous")

		c.JSON(200, gin.H{
			"status":  "posted",
			"message": message,
			"nick":    nick,
		})
	})
```

### pureJson

```go
// 提供 unicode 实体
	r.GET("/json", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"html": "<b>Hello, world!</b>",
		})
        //{"html":"\u003cb\u003eHello, world!\u003c/b\u003e"}
	})
	
	// 提供字面字符
	r.GET("/purejson", func(c *gin.Context) {
		c.PureJSON(200, gin.H{
			"html": "<b>Hello, world!</b>",
		})
        //{"html":"<b>Hello, world!</b>"}
	})
```

### Query和postForm

```go
id := c.Query("id")
		page := c.DefaultQuery("page", "0")
		name := c.PostForm("name")
		message := c.PostForm("message")
```

### SecureJSON 

> SecureJSON 是一个用于在 Go 语言中安全地处理 JSON 数据的库。它提供了一些功能来防止常见的安全漏洞，例如 JSON 注入和数据篡改。

```go
names := []string{"lena", "austin", "foo"}

		// 将输出：while(1);["lena","austin","foo"]
		c.SecureJSON(http.StatusOK, names)
```

### XML/JSON/YAML/ProtoBuf 渲染

```go
var msg struct {
			Name    string `json:"user"`
			Message string
			Number  int
		}
		msg.Name = "Lena"
		msg.Message = "hey"
		msg.Number = 123
		// 注意 msg.Name 在 JSON 中变成了 "user"
		// 将输出：{"user": "Lena", "Message": "hey", "Number": 123}
		c.JSON(http.StatusOK, msg)
```

### 上传文件

`multipart/form-data` 支持文件上传，可以通过表单将文件数据发送到服务器。

 `application/x-www-form-urlencoded` 不支持文件上传，只能传输文本数据

```go
r.MaxMultipartMemory = 8 << 20  // 8 MiB
	r.POST("/upload", func(c *gin.Context) {
		// 单文件
		file, _ := c.FormFile("file")
		log.Println(file.Filename)

		dst := "./" + file.Filename
		// 上传文件至指定的完整文件路径
		c.SaveUploadedFile(file, dst)

		c.String(http.StatusOK, fmt.Sprintf("'%s' uploaded!", file.Filename))
        
        //多文件上传
		form, _ := c.MultipartForm()
		files := form.File["upload[]"]

		for _, file := range files {
			log.Println(file.Filename)

			// 上传文件至指定目录
			c.SaveUploadedFile(file, dst)
		}
		c.String(http.StatusOK, fmt.Sprintf("%d files uploaded!", len(files)))
	})
//多文件上传
```

### 中间件

使用 `gin.Default` 不使用 `gin.New`

### 优雅退出程序

```go
srv := &http.Server{
		Addr:    ":8080",
		Handler: router,
	}

	go func() {
		// 服务连接
		if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
			log.Fatalf("listen: %s\n", err)
		}
	}()

	// 等待中断信号以优雅地关闭服务器（设置 5 秒的超时时间）
	quit := make(chan os.Signal)
	signal.Notify(quit, os.Interrupt)
	<-quit
	log.Println("Shutdown Server ...")

	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	if err := srv.Shutdown(ctx); err != nil {
		log.Fatal("Server Shutdown:", err)
	}
	log.Println("Server exiting")
```

`signal.*Notify*(chan quit, os.Interrupt)`可以监听系统执行一些操作时的信号

### 使用`BasicAuch`中间件

```go
authorized := r.Group("/admin", gin.BasicAuth(gin.Accounts{
		"foo":    "bar",
		"austin": "1234",
		"lena":   "hello2",
		"manu":   "4321",
	}))
authorized.GET("/secrets", func(c *gin.Context) {
		// 获取用户，它是由 BasicAuth 中间件设置的
		user := c.MustGet(gin.AuthUserKey).(string)
		if secret, ok := secrets[user]; ok {
			c.JSON(http.StatusOK, gin.H{"user": user, "secret": secret})
		} else {
			c.JSON(http.StatusOK, gin.H{"user": user, "secret": "NO SECRET :("})
		}
	})
```

### 使用中间件

- 单个路由中间件 `r.group("/",中间件。。。)` `r.get("/",。。。中间件)`
- 路由中间件 `r.use(...中间件)`

### 只绑定Query`ShouldBindQuery`

如果使用`r.Any`定义了方法，`ShouldBindQuery`方法只允许从query 获取请求参数

在`golang`中使用`goroutine`时,需要使用`c.Copy`获取当前上下文

### 记录日志

```go
gin.DisableConsoleColor()
	
	f,err := os.Create("ginServer.log")
	if err != nil {
		println(err)
	}
		gin.DefaultWriter = io.MultiWriter(f)
	//需要输出到多个输出使用这个方法
	//gin.DefaultWriter = io.MultiWriter(f,os.Stdout) 
	r := gin.Default()
```

- 修改日志格式

```go
gin.DebugPrintRouteFunc
```

### [将 request body 绑定到不同的结构体中](https://gin-gonic.com/zh-cn/docs/examples/bind-body-into-dirrerent-structs/)

### 自定义HTTP配置

```go
func main() {
	router := gin.Default()
	http.ListenAndServe(":8080", router)
}

func main() {
	router := gin.Default()

	s := &http.Server{
		Addr:           ":8080",
		Handler:        router,
		ReadTimeout:    10 * time.Second,
		WriteTimeout:   10 * time.Second,
        //位运算 限制请求头的大小 防止恶意用户发送过大的请求头导致服务端资源耗尽
		MaxHeaderBytes: 1 << 20,
	}
	s.ListenAndServe()
}
```

### 自定义验证器

```go
var bookableDate validator.Func = func(fl validator.FieldLevel) bool {
	date, ok := fl.Field().Interface().(time.Time)
	if ok {
		today := time.Now()
		if today.After(date) {
			return false
		}
	}
	return true
}

type Booking struct {
    //使用bookabledate验证规则 验证是否是今天之前
	CheckIn  time.Time `form:"check_in" binding:"required,bookabledate" time_format:"2006-01-02"`
    //gtField 表示CheckIn的值不能大于 该值
	CheckOut time.Time `form:"check_out" binding:"required,gtfield=CheckIn,bookabledate" time_format:"2006-01-02"`
}
```

### `errgroup.Group`实现对多个server的错误监控

```go
	g.Go(func() error {
		return server01.ListenAndServe()
	})

	g.Go(func() error {
		return server02.ListenAndServe()
	})

	if err := g.Wait(); err != nil {
		log.Fatal(err)
	}
```

### 重定向

```go
//路由重定向
c.Request.URL.Path = "/long_async"
r.HandleContext(c)
//post方法重定向
c.Redirect(http.StatusFound, "https://www.baidu.com")
```

