# go-kit

> 是一个微服务工具的集合，利用它提供的API和规范可以创建健壮，可维护性高的微服务体系

### 三层架构

#### Transpot 

主要负责与HTTP,gRPC,thrift 等相关的逻辑

外部请求过来的时候如何对外部请求进行解码，需要对requset进行decode

#### EndPoint	

定义Rquest和Response格式，并可以使用装饰器包装函数(如：日志记录功能)，以此来实现各种中间件的嵌套

#### service

这里是我们的业务接口

### 服务注册

#### 手动注册

p.json

```json
{
    "ID":"userservice",
    "Name":"userservice",
    "Tags":[
        "primary"	
    ],
    "Address":"192.168.0.25",
    "Port":8080,
    "Check":{
        "HTTP":"http://192.168.0.25/health",
    	"Interval":"5s"
    }
}
```

注册

```shell
curl --request PUT  --data @p.json localhost:8500/v1/agent/service/register
```

反注册

```shell
curl --request PUT   localhost:8500/v1/agent/service/deregister/userservice
```

#### 删除服务

获取服务列表

```shell
curl --request get localhost:8500/v1/agent/services
```

删除服务,需要用到上面查到的服务ID

```shell
curl --request PUT   localhost:8500/v1/agent/service/deregister/服务ID,
```

#### 熔断

隔离远程服务请求，防止级联故障

实现就是请求的时候，如果远程请求超时，就请求自己本地的方法

hystrix  会把请求或者处理都封装成一个command ，并且委托给hystrix  进行执行。

在执行过程中设置超时时间，服务降级，最大连接数等。

###### 熔断器的部署

熔断可以部署在客户端也可以部署在网关中

#### jwt

JWT的三个部分如下。JWT头、有效载荷和签名

##### JWT头：

​	JWT头部分是一个描述JWT元数据的JSON对象，签名使用的算法，令牌的类型

##### 有效载荷(payload)

​	是JWT的主体内容部分，也是一个JSON对象，包含需要传递的数据。 JWT指定七个默认字段供选择，

​	iss：发行人

​	exp：到期时间

​	sub：主题

​	aud：用户

​	nbf：在此之前不可用

​	iat：发布时间

​	jti：JWT ID用于标识该JWT

默认情况下JWT是未加密的，任何人都可以解读其内容，因此不要构建隐私信息字段，存放保密信息，以防止信息泄露

##### 签名

签名哈希部分是对上面两部分数据签名，通过指定的算法生成哈希，以确保数据不会被篡改。首先 需要指定一个密码（secret）。该密码仅仅为保存在服务器中，并且不能向用户公开。然后，使用标头中指定的签名算法（默认情况下为HMAC SHA256）根据以下公式生成签名。

HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload),

##### 示例代码

```go
package main

import (
	"fmt"
	"github.com/dgrijalva/jwt-go"
	"go-kit-test/test_test/utils"
	"io/ioutil"
	"log"
	"time"
)

// 保存的用户信息
type UserClaim struct {
	jwt.StandardClaims
	Uname string `json:"username"`
}

func main_1() {

	sec:=[]byte("123abc")
	// 使用对称加密hs256
	// 用户登录的时候会把用户密码传递过来，数据库验证通过后，生产token对象。UserClaim 可以理解成就是有效载荷(payload)
	un:=UserClaim{Uname:"wallace"}
	// 设置过期时间
	un.ExpiresAt=time.Now().Add(time.Second*10).Unix()
	token_obj:=jwt.NewWithClaims(jwt.SigningMethodHS256,un)
	// 对token进行加密，生产token字符串,把下面生产的json web token发送给用户
	token,err:=token_obj.SignedString(sec)
	fmt.Println(token,err)

	//生产token后，前端会把token放到本地session中，后面请求接口的时候都会带着这个token.

	// 验证
	pase_token,_:=jwt.Parse(token, func(token *jwt.Token) (i interface{}, e error) {
		// 返回的是秘钥和错误
		return sec,e
	})
	// 表示验证token是否解析通过了
	if pase_token.Valid{
		fmt.Println(pase_token.Claims)
	}
	// 也可以把token中的内容解析到结构体中
	uc:=UserClaim{}
	getToken,err:=jwt.ParseWithClaims(token,&uc, func(token *jwt.Token) (i interface{}, e error) {
		return sec,err
	})
	// 如果token过去，这个getToken.Valid 的值使false
	if getToken!=nil&&getToken.Valid{
		// 会把解析的结果放到Claims中。
		// getToken.Claims.(*UserClaim)断言getToken.Claims 是否是UserClaim类型
		fmt.Println(getToken.Claims.(*UserClaim).Uname)
		// uc中也存储的有解析结果。
		fmt.Println(uc)
	}else if ve,ok:=err.(*jwt.ValidationError);ok{
		if ve.Errors&jwt.ValidationErrorMalformed!=0{
			fmt.Println("错误的token")
		}else if ve.Errors & (jwt.ValidationErrorExpired|jwt.ValidationErrorNotValidYet)!=0{
			fmt.Println("token 过期或未启用")
		}else {
			fmt.Println("couldn't handle this token",err)
		}
	}else {
		fmt.Println("无法解析此token",err)
	}



}
// 分对称加密
func main()  {
	// 生成公私钥
	err:=utils.GenRSAPubAndPri(1024,"./pem")
	if err != nil {
		return
	}
	priKeyBytes,err:=ioutil.ReadFile("./pem/private.pem")
	if err!=nil{
		log.Fatal("私钥文件读取失败")
	}
	// 需要对私钥进行封装
	pri,err:=jwt.ParseRSAPrivateKeyFromPEM(priKeyBytes)
	if err!=nil{
		log.Fatal("私钥文件不正确")
	}
	pubKeyBytes,err:=ioutil.ReadFile("./pem/public.pem")
	if err!=nil{
		log.Fatal("公钥文件读取失败")
	}
	// 需要对公钥进行封装
	pubKey,err:=jwt.ParseRSAPublicKeyFromPEM(pubKeyBytes)
	if err!=nil{
		log.Fatal("公钥文件不正确")
	}

	// 分对称加密，SigningMethodRS256
	token_obj:=jwt.NewWithClaims(jwt.SigningMethodRS256,UserClaim{Uname:"wallace"})
	token,err:=token_obj.SignedString(pri)
	fmt.Println(token,err)


	// 解析token中的内容
	uc:=UserClaim{}
	getToken,_:=jwt.ParseWithClaims(token,&uc, func(token *jwt.Token) (i interface{}, e error) {
		return pubKey,err
	})
	if getToken.Valid{
		// 会把解析的结果放到Claims中。
		// getToken.Claims.(*UserClaim)断言getToken.Claims 是否是UserClaim类型
		fmt.Println(getToken.Claims.(*UserClaim).Uname)
		// uc中也存储的有解析结果。
		fmt.Println(uc)
	}
}
```

