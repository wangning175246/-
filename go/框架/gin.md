#### 文档

> https://www.jianshu.com/p/98965b3ff638/

#### 基本使用

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r:=gin.Default() // 返回的是路由引擎
	r.GET("/hello",sayHello)
	r.Run(":8080")
}

func sayHello(context *gin.Context) {
	data:=map[string]string{
		"data":"wallace",
	}
	context.JSON(http.StatusOK,data) // 返回json数据
}
```

模板的使用

>  https://www.liwenzhou.com/posts/Go/go_template/

#### json数据的返回

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r:=gin.Default() // 返回的是路由引擎

	r.GET("/hello",sayHello)
	r.Run(":8080")
}

func sayHello(context *gin.Context) {
	// 定义一个map
	//data:=map[string]string{
	//	"data":"wallace",
	//}
	// 使用go  内置的ma
	data:=gin.H{"name":"wallace","Age":25,"Message":"hello word"}
	// 结构体的方式
	//type msg struct{
	//	Name string
	//	Age int
	//	Message string
	//}
	//data:=msg{
	//	Name:    "wallace",
	//	Age:     25,
	//	Message: "hell word",
	//}
	context.JSON(http.StatusOK,data)
}
```

#### 中间件

> https://jinfeijie.cn/post-837.html

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)


func main(){
	router:=gin.Default()
	router.Use(Validate())  //使用validate()中间件身份验证
	router.GET("/",Service1)
	router.Run(":8080")  //localhost:8989/
}

func Service1(c *gin.Context){
	c.JSON(http.StatusOK,gin.H{"message":"你好，欢迎你"})
}

func Validate() gin.HandlerFunc{
	return func(c *gin.Context){
		//这一部分可以替换成从session/cookie中获取，
		username:=c.Query("username")
		password:=c.Query("password")

		if username=="ft" && password =="123"{
			c.JSON(http.StatusOK,gin.H{"message":"身份验证成功"})
			c.Next()  //该句可以省略，写出来只是表明可以进行验证下一步中间件，不写，也是内置会继续访问下一个中间件的,具体可以看Next的源码
		}else {
// 标识如果执行了这个,就不会向执行中间后面的中间件或者路由了,当前中间间执行完成后会直接返回给用户数据
//主要是设置c.index的值，中间的执行就是根据c.index，判断下一个需要执行的中间件。
			c.Abort() 
			c.JSON(http.StatusUnauthorized,gin.H{"message":"身份验证失败"})
			return// return也是可以省略的，这个return 只是表示这个函数执行完了。并不会终止请求向下执行
		}
	}
}
```

