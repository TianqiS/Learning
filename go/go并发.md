# go并发

### 并发模型-MPG

1. M 代表着一个内核线程，也可以称为一个工作线程。goroutine就是跑在M之上的
2. P 代表着(Processor)处理器 它的主要用途就是用来执行goroutine的，一个P代表执行一个Go代码片段的基础（可以理解为上下文环境），所以它也维护了一个可运行的goroutine队列，和自由的goroutine队列，里面存储了所有需要它来执行的goroutine。
3. G 代表着goroutine 实际的数据结构(就是你封装的那个方法)，并维护者goroutine 需要的栈、程序计数器以及它所在的M等信息。
4. Seched 代表着一个调度器 它维护有存储空闲的M队列和空闲的P队列，可运行的G队列，自由的G队列以及调度器的一些状态信息等。

![img](https://pic4.zhimg.com/80/v2-4478c5c104fba41896d9edb7d66b83cb_720w.jpg)

我们在看上面这个图，图中P正在执行的Goroutine为蓝色的，处于待执行状态的Goroutine为灰色的，灰色的Goroutine形成了一个队列runqueues。

![preview](https://pic1.zhimg.com/v2-fe6b39f9ff90f8ab72e1490b31ee9a44_r.jpg)

在这里，当一个P关联多个G时，就会处理G的执行顺序，就是并发，当一个P在执行一个协程工作时，其他的会在等待，当正在执行的协程遇到阻塞情况，例如IO操作等，go的处理器就会去执行其他的协程，因为对于类似IO的操作，处理器不知道你需要多久才能执行结束，所以他不回去等你执行完。

上面我们看着go的并发好像是抢占式的，事实上go的协程是**非抢占式的**，由协程主动交出控制权，也就是说，上面在发生IO操作时，并不是调度器强制切换执行其他的协程，而是当前协程交出了控制权，调度器才去执行其他协程。我们列举一下goroutine可能切换的点：

- I/O，select
- channel
- 等待锁
- runtime.Gosched()

这些点是go协程可能切换的地方，但是并不是一定切换的。

正是因为是**非抢占式**的，所以才轻松的构造上万的协程，如果是抢占式，那么就会在切换任务时，保存当前的上下文环境，因为当前线程如果正在做一件事，做到一半，我们就强制停止，这时我们就必须多保存很多信息，避免再次切换回来时任务出错。

线程是操作系统层面的多任务，而go的协程属于编译器层面的多任务，go有自己的调度器来调度。一个协程在哪个线程上是不确定的，这个是由调度器来决定的，多个协程可能在一个或多个线程上运行。

由于协程是用户调度的，所以不会出现执行一半的代码片段被强制中断了，因此无需原子操作锁。

### 协程

Go在语言级别原生支持并发操作，这在现代众多基于线程并发的其他语言来看是比较鹤立鸡群的。在Go中最基本的并发任务单元是一种称为goroutine的东西，我们把它叫做协程或go程，其开一个并发任务简单到令人发指，只需go关键字，就能让一个函数成为并发任务。

go的协程有如下几个特点：

- Go的协程是一个轻量级的线程，多个协程可能运行在一个或者多个线程上；
- Go的协程是非抢占式多任务处理，需要由协程主动交出控制权，这与其他抢占型的线程并发方式不同，这种非抢占式设计减少了许多cpu调度切换的开销；
- Go的协程是一个虚拟机层面的多任务处理，它基于Go的并发调度器，其调度是go运行时自动管理的，对用户不可见，用户只需管理业务层面的并发即可。
- Go中所有的调用栈都是一个协程，main()函数就是主协程，如无开辟其他协程所有任务都在主协程运行。

#### go协程特性：主死从随

主协程(main)没有阻塞的执行完，程序结束，子协程无论有无执行完都会结束。

1. main 函数退出，所有协程退出
2. 协程无父子关系，即在父协程开启新的协程，若父协程退出，不影响子协程

```go
func main() {
   go func() {
      for i := 1; i <= 10; i++ {
         fmt.Println("子协程在输出", i)
         //time.Sleep(time.Second)
      }
   }()

   for i := 0; i < 50; i++ {
      fmt.Println("主协程输出", i)
      //time.Sleep(time.Second) //就算主协程不进行阻塞，从协程也会执行
   }
}
```

上述代码中主协程执行完从协程也会执行完

#### go协程特性：出让协程资源

go的协程是非抢占式的，占有cpu资源的协程如无主动让出，其他协程只能等待：使用`runtime.Gosched()`进行控制

#### go协程特性：设置并查看可用cpu核心数

在如今的多核时代，go程序能够充分利用cpu核心数，其runtime包中提供可设置查看可用cpu核心数的函数：`runtime.GOMAXPROCS()`

```go
func BaseGoroutine04() {

    numCPU := runtime.NumCPU()
    fmt.Println("CPU逻辑核心数：", numCPU)

    //GOMAXPROCS设置可同时执行的最大CPU数，并返回先前的设置
    prevNum := runtime.GOMAXPROCS(2)
    fmt.Println("设置CPU核心数为2，设置前为", prevNum)

}
```

#### go协程特性：协程退出

使用runtime.Goexit()可让协程退出，父协程和子协程各自退出的后果不同，子协程退出后提前执行defer，非善终（协程不会正常返回），父协程退出会令子协程失去牵制，主从皆死程序崩溃。

```go
func main() {
   go func() {
      defer func() {
         fmt.Println("wozoule")
      }()
      for i:= 0; i < 5; i++ {
         if i == 3 {
            fmt.Println("i go out")
            runtime.Goexit()
         }
         fmt.Println("printing", i)
      }
   }()

   for i := 0; i < 10; i++ {
      fmt.Println("main thread printing", i)
   }
   time.Sleep(time.Second)
}
```

#### go协程特性：协程间按调度器队列执行，公平使用资源

非抢占式的并发模型不会主动占有cpu资源，开启协程后其会在调度器中的协程队列排队，除非别人出让资源，否则按队列执行

```go
func main() {
   go func() {
      for i := 1; i <= 10; i++ {
         fmt.Println("协程1：自己排队！")
         time.Sleep(time.Nanosecond)
      }
   }()

   go func() {
      for i := 1; i <= 10; i++ {
         fmt.Println("协程2：自己排队！")
         time.Sleep(time.Nanosecond)
      }
   }()
   time.Sleep(time.Second)
}
```

