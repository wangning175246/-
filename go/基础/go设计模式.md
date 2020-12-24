#### 单例模式

单例模式主要时为了加载配置，或者其他资源，避免加载多次

懒汉模式 用的时候加载

```go
package main
type config struct {
}
var cfg *config
func GetInstane() *config {
	if cfg == nil{
		cfg= new(config)
		return cfg
	}
	return cfg
}
```

饿汉模式 系统初始化的时候加载

```go
package main
type config struct {
}
var cfg *config =new(config)
func GetInstane() *config {
	 return cfg
}
```

#### 选项模式

```go
package main



// 定义一个配置结构体
type Opetions struct {
	opetions1 string
	opetions2 string
	opetions3 int
	opetions4 int
}

// 我们如果定义结构中的内容进行初始化，
// 1、使用下面这种方式，如果配置增加了，则初始化的需要原来的代码，配置顺序修改了，参数的顺序也需要修改
//func InitOptions(配置1，配置2，配置3，配置4)
// 2. 使用下面的这种方式,传递切片进去，通过切片的下标和参数的进行对应，如果配置顺序发生改变则代码需要改变
//func InitOptions(opts ...interface{})
// 3、使用如下模式，配置性会提高，可扩展展性提高
type Option func(opts *Opetions)

func InitOptions1(opts ...Option)  {
	options:=&Opetions{}
	for _,opt:=range opts{
		opt(options)
	}
}
func WithStringOption1(str string)  Option{
	return func(opts *Opetions) {
		opts.opetions1=str
	}
}

func WithStrinOption2(str string) Option {
	return func(opts *Opetions) {
		opts.opetions2=str
	}
}
func WithIntOption3(in int) Option  {
	return func(opts *Opetions) {
		opts.opetions3=in
	}
}
func WithIntOption4(in int) Option {
	return func(opts *Opetions) {
		opts.opetions4 = in
	}
}

func main()  {
	InitOptions1(
		WithStringOption1("options1"),
		WithStringOption1("options1"),
		WithIntOption3(1),
		WithIntOption4(2))
}
```

#### 中间件设计模式

https://colobu.com/2019/08/21/decorator-pattern-pipeline-pattern-and-go-web-middlewares/

```go
package main

import "fmt"

type Fn func(x, y int) int
func (fn Fn) Chain(f Fn) Fn {
	return func(x, y int) int {
		fmt.Println(fn(x, y))
		return f(x, y)
	}
}
func add(x, y int) int {
	fmt.Printf("%d + %d = ", x, y)
	return x + y
}
func minus(x, y int) int {
	fmt.Printf("%d - %d = ", x, y)
	return x - y
}
func mul(x, y int) int {
	fmt.Printf("%d * %d = ", x, y)
	return x * y
}
func divide(x, y int) int {
	fmt.Printf("%d / %d = ", x, y)
	return x / y
}
func main() {
	//var result = Fn(add).Chain(Fn(minus)).Chain(Fn(mul)).Chain(Fn(divide))(10, 5)
	var result = Fn(add).Chain(Fn(minus)).Chain(Fn(mul))(10, 5)
	fmt.Println(result)
}
############# 结果 #############
10 + 5 = 15
10 - 5 = 5
10 * 5 = 50
```

