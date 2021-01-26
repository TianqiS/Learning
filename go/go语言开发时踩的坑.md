### go语言开发时踩的坑

---

#### go get下载不来

解决方法：添加如下代码

`go env -w GOPROXY=https://goproxy.cn,direct`

#### Go moudles的使用

#### 错误包裹模块errorx的使用

#### select语法的应用

select 是 Go 中的一个控制结构，类似于用于通信的 switch 语句。每个 case 必须是一个通信操作，要么是发送要么是接收。

select 随机执行一个可运行的 case。如果没有 case 可运行，它将阻塞，直到有 case 可运行。一个默认的子句应该总是可运行的。

```go
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s);
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
```



