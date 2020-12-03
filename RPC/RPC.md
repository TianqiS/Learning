### RPC

---

gRPC 是 Google 基于 HTTP/2 以及 protobuf 的，要了解 gRPC 协议，只需要知道 gRPC 是如何在 HTTP/2 上面传输就可以了。

gRPC 通常有四种模式，**unary，client streaming，server streaming** 以及 bidirectional streaming，对于底层 HTTP/2 来说，它们都是 stream，并且仍然是一套 request + response 模型。

gRPC 的基石就是 HTTP/2，然后在上面使用 protobuf 协议定义好 service RPC。虽然看起来很简单，但如果一门语言没有 HTTP/2，protobuf 等支持，要支持 gRPC 就是一件非常困难的事情了。



---

参考资料：

https://www.jianshu.com/p/48ad37e8b4ed