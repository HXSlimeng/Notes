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

