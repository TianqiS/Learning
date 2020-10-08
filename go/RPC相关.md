### Go语言中的RPC

rpc概念：RPC（Remote Procedure Call，远程过程调用）是一种通过网络请求从远程服务器调用服务，而不需要了解底层网络细节的应用程序通信协议。

> 当执行一个 RPC 调用时，客户端程序首先会发送一个带有参数的请求到服务端，然后等待服务端响应；在服务端，服务进程保持监听状态，当客户端请求到达时，服务端通过解析请求参数计算出结果，并向客户端发送响应信息，然后继续等待下一个客户端请求。客户端接收到来自服务端的响应信息后，可以执行相应的业务逻辑，也可以继续进行其它 RPC 调用。

在go语言中使用`net/rpc`来编写RPC服务，在 RPC 服务端，需要将这个对象注册为可访问的服务，之后该对象的公开方法就能够以远程的方式提供访问。

一个 RPC 服务端可以注册多个不同类型的对象，**但不允许注册同一类型的多个对象**。此外，一个对象只有满足以下这些条件的方法，才能被 RPC 服务端设置为可提供远程访问：

- 必须是在对象外部可公开调用的方法（首字母大写）；
- 必须有两个参数，且参数的类型都必须是包外部可以访问的类型或者是 Go 内建支持的类型；
- 第二个参数必须是一个指针；
- 方法必须返回一个 `error` 类型的值。

```go
func (t *T) MethodName(argType T1, replyType *T2) error
```

其中，类型 `T`、`T1` 和 `T2` 分别对应服务对象所属类型、请求类型和响应类型，它们默认都会使用 Go 语言内置的 `encoding/gob`包进行编码解码。

该方法（`MethodName`）的第一个参数表示由 RPC 客户端传入的请求参数，第二个参数表示要返回给 RPC 客户端的响应结果，最后返回的是一个 `error` 类型的值表示错误信息。

#### 示例代码目录结构

接下来，我们编写一个简单的 RPC 服务端与客户端实现示例，来演示 RPC 调用，初始化示例代码目录结构如下：

```go
|---golang
      |---src
           |---demo
                 |---rpc
                      |---client.go
                      |---server.go
                      |---utils
                            |---common.go
```



其中 `golang` 是项目根路径，也是 `GOPATH` 指向路径，然后在 `rpc` 目录下，创建一个 `server.go` 用于存放 RPC 服务端代码，创建一个 `client.go` 用于存放 RPC 客户端代码，为了方便执行代码，我们将这两个文件所在包都设置为 `main`，然后在 `rpc` 目录下创建一个 `utils/common.go` 文件，用于存放客户端和服务端共用的类、方法和变量。

初始化 `common.go` 代码如下：

```go
package utils

type Args struct {
	A, B int
}
```



其中定一个了 `Args` 类，用于在 RPC 服务端和客户端中定义请求类型并设置请求参数 `A`、`B`。

#### RPC 服务端实现

接下来，在 `rpc/server.go` 中 RPC 服务端代码实现。首先编写一个 `MathService` 表示服务对象类型，该服务可以对客户端提供乘法和除法远程方法调用：

```go
package main

import (
   "demo/rpc/utils"
   "errors"
   "log"
	"net"
	"net/http"
   "net/rpc"
)

type MathService struct {

}

func (m *MathService) Multiply(args *utils.Args, reply *int) error {
	*reply = args.A * args.B
	return nil
}

func (m *MathService) Divide(args *utils.Args, reply *int) error {
	if args.B == 0 {
		return errors.New("除数不能为0")
	}
	*reply = args.A / args.B
	return nil
}

func main() {
    // 启动 RPC 服务端
}
```



请求对象类型是 `utils.Args`，响应类型就是个内置整型，通过 `Multiply` 方法定义乘法操作，通过 `Divide` 方法定义除法操作，这两个方法首字母都是大写的，意味着可以被远程客户端调用。

然后我们在 `main` 函数中注册 `MathService` 服务对象并启动该 RPC 服务：

```go
func main() {
	math := new(MathService)
	rpc.Register(math)
	rpc.HandleHTTP()
	listener, err := net.Listen("tcp", ":8080")
	if err != nil {
		log.Fatal("启动服务监听失败:", err)
	}
	err = http.Serve(listener, nil)
	if err != nil {
		log.Fatal("启动 HTTP 服务失败:", err)
	}
}
```



在这段代码中，我们将 `MathService` 服务对象通过 `rpc.Register` 方法注册到服务端，然后以 HTTP 服务器作为 RPC 服务端，并指定端口为 `8080`，最后调用 `http.Serve` 启动这个 HTTP 服务器，等待客户端请求。至此，RPC 服务端代码就编写好了。

#### RPC 客户端实现

接下来，我们在 `client.go` 中编写 RPC 客户端调用代码，调用服务端提供的远程方法之前，需要先和 RPC 服务端建立连接。这里我们通过 `net/rpc` 包提供的 `DialHTTP` 方法建立与指定 IP 地址和端口的 RPC 服务器的连接：

```go
package main

import (
	"demo/rpc/utils"
   "fmt"
   "log"
   "net/rpc"
)

func main()  {
	var serverAddress = "localhost"
	client, err := rpc.DialHTTP("tcp", serverAddress + ":8080")
	if err != nil {
		log.Fatal("建立与服务端连接失败:", err)
	}
}
```



连接建立成功之后，客户端就可以调用服务端提供的远程方法了。首先，我们来看同步调用的实现：

```go
args := &utils.Args{10,10}
var reply int
err = client.Call("MathService.Multiply", args, &reply)
if err != nil {
	log.Fatal("调用远程方法 MathService.Multiply 失败:", err)
}
fmt.Printf("%d*%d=%d\n", args.A, args.B, reply)
```



同步调用通过在连接对象上调用 `Call` 方法实现，所谓同步调用指的是只有接收完 RPC 服务端的处理结果之后才可以继续执行后面的程序。此外，还可以通过 `Go` 方法以异步方式进行 RPC 调用，具体实现代码如下：

```go
divideCall := client.Go("MathService.Divide", args, &reply, nil)
for {
	select {
	case <-divideCall.Done:
		fmt.Printf("%d/%d=%d\n", args.A, args.B, reply)
		return
	}
}
```



异步调用时，RPC 客户端程序无需等待服务端的结果即可执行后面的程序，当接收到 RPC 服务端的处理结果时，再对其进行相应的处理。无论同步调用还是异步调用，都必须指定要调用的远程服务和方法、以及客户端请求参数的指针和接收处理结果参数的指针，对于异步调用，还要传入一个用于标识调用是否完成的通道参数，这里我们将其设置为 `nil`。

#### 测试远程 RPC 调用

在 `rpc` 目录下，先运行 `go run server.go` 启动 RPC 服务端，没有报错说明启动成功：

![启动 RPC 服务端](https://qcdn.xueyuanjun.com/storage/uploads/images/gallery/2019-11/scaled-1680-/image-1573055088161.png)

然后新开一个 Terminal 窗口，进入 `rpc` 目录，运行客户端调用代码：

![运行 RPC 客户端](https://qcdn.xueyuanjun.com/storage/uploads/images/gallery/2019-11/scaled-1680-/image-1573055109025.png)

如果打印以上结果，则说明我们的 RPC 远程服务调用成功。

