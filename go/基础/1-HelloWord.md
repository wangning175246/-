## hello word

go/src/examples/main

```go
# 声明包名
package main

# 导入模块
import "fmt"

# 声明mian函数
func main()  {
	fmt.Print("hello word")
}
```

执行都是从main包的main函数开始执行

包：一个目录是一个包，一个目录中可以有多个文件，这多个文件属性同一个包(package后面的名字相同)。

## 直接运行

``` shell
cd go/src/
go run examples/main
hello word
```

## 编译运行

#### 直接编译源文件

```shell
cd go/src/examples/main/main.go
go build main.go
./main
```

直接编译文件的时候，如果文件直接有依赖需要指定编译多个文件。后面可以指定多个文件，

#### 编译包

```shell
cd go/
go build -o hello  examples/main
./hello
hello word
```

go build 一般是在project GOPATH路径下执行的，会去自动找路径下src，所以一般编译的时候指定路径的是从src后面开始指定路径，指定到main包(main函数所在的路径)为止





