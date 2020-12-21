### type类型定义

[原文地址](https://www.jianshu.com/p/a02cf41c0520)

---

##### 一、类型定义--------节选自《Go语言圣经》第69页

为了说明类型声明，我们将不同温度单位分别定义为不同的类型：



```go
// Package tempconv performs Celsius and Fahrenheit temperature computations.
package tempconv

import "fmt"

type Celsius float64 // 摄氏温度
type Fahrenheit float64 // 华氏温度

const (
    AbsoluteZeroC Celsius = -273.15 // 绝对零度
    FreezingC Celsius = 0 // 结冰点温度
    BoilingC Celsius = 100 // 沸水温度
)

func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
```

我们在这个包声明了两种类型：**Celsius和Fahrenheit分别对应不同的温度单位。它们虽然有着相同的底层类型float64，但是它们是不同的数据类型，因此它们不可以被相互比较或混在一个表达式运算。刻意区分类型，可以避免一些像无意中使用不同单位的温度混合计算导致错误；**因此需要一个类似Celsius(t)或Fahrenheit(t)形式的显式转型操作才能将float64转为对应的类型。Celsius(t)和Fahrenheit(t)是类型转换操作，它们并不是函数调用。类型转换不改变值本身，但是会使它们的语义发生变化。另一方面，CToF和FToC两个函数则是对不同温度单位下的温度进行换算，它们会返回不同的值。

对于每一个类型T，都有一个对应的类型转换操作T(x)，用于将x转为T类型（译注：如果T是指针类型，可能会需要用小括弧包装T，比如 (*int)(0)  ）。只有当两个类型的底层基础类型相同时，才允许这种转型操作，或者是两者都是指向相同底层结构的指针类型，这些转换只改变类型而不会影响值本身。如果x是可以赋值给T类型的值，那么x必然也可以被转为T类型，但是一般没有这个必要。

数值类型之间的转型也是允许的，并且在字符串和一些特定类型的slice之间也是可以转换的，在下一章我们会看到这样的例子。这类转换可能改变值的表现。例如，将一个浮点数转为整数将丢弃小数部分，将一个字符串转为 []byte  类型的slice将拷贝一个字符串数据的副本。在任何情况下，运行时不会发生转换失败的错误（译注: 错误只会发生在编译阶段）。

底层数据类型决定了内部结构和表达方式，也决定是否可以像底层类型一样对内置运算符的支持。这意味着，Celsius和Fahrenheit类型的算术运算行为和底层的float64类型是一样的，正如我们所期望的那样。



```objectivec
fmt.Printf("%g\n", BoilingC-FreezingC) // "100" °C
boilingF := CToF(BoilingC)
fmt.Printf("%g\n", boilingF-CToF(FreezingC)) // "180" °F
fmt.Printf("%g\n", boilingF-FreezingC) // compile error: type mismatch
```

比较运算符 ==  和 <  也可以用来比较一个命名类型的变量和另一个有相同类型的变量，或有着相同底层类型的未命名类型的值之间做比较。但是如果两个值有着不同的类型，则不能直接进行比较：



```swift
var c Celsius
var f Fahrenheit
fmt.Println(c == 0) // "true"
fmt.Println(f >= 0) // "true"
fmt.Println(c == f) // compile error: type mismatch
fmt.Println(c == Celsius(f)) // "true"!
```

注意最后那个语句。尽管看起来想函数调用，但是Celsius(f)是类型转换操作，它并不会改变值，仅仅是改变值的类型而已。测试为真的原因是因为c和g都是零值。

##### 二、类型定义让代码更加简洁

使用类型定义定义出来的类型与原类型不相同，所以不能使用新类型变量赋值给原类型变量，除非使用强制类型转换。下面来看一段示例代码，根据string类型，定义一种新的类型，新类型名称是name：



```go
type name string
```

为什么要使用类型定义呢？类型定义可以在原类型的基础上创造出新的类型，有些场合下可以使代码更加简洁，如下边示例代码：



```go
package main

import (
    "fmt"
)

// 定义一个接收一个字符串类型参数的函数类型
type handle func(str string)

// exec函数，接收handle类型的参数
func exec(f handle) {
    f("hello")
}

func main() {
    // 定义一个函数类型变量，这个函数接收一个字符串类型的参数
    var p = func(str string) {
        fmt.Println("first", str)
    }
    exec(p)

    // 匿名函数作为参数直接传递给exec函数
    exec(func(str string) {
        fmt.Println("second", str)
    })
}
```

输出信息是：



```undefined
first hello
second hello
```

上边的示例是类型定义的一种简单应用场合，如果不使用类型定义，那么想要实现上边示例中的功能，应该怎么书写这段代码呢？



```go
// exec函数，接收handle类型的参数
func exec(f func(str string)) {
    f("hello")
}
```

exec函数中的参数类型，需要替换成func(str string)了，咋一看去也不复杂，但是假如exec接收一个需要5个参数的函数变量呢？是不是感觉参数列表就会很长了。



```go
func exec(f func(str string, str2 string, num int, money float64, flag bool)) {
    f("hello")
}
```

从上边的代码可以发现，exec函数的参数列表可读性变差了。下边再来看看使用类型定义是怎么实现这个功能：



```go
package main

import (
    "fmt"
)

// 定义一个需要五个参数的函数类型
type handle func(str string, str2 string, num int, money float64, flag bool)

// exec函数，接收handle类型的参数
func exec(f handle) {
    f("hello", "world", 10, 11.23, true)
}

func demo(str string, str2 string, num int, money float64, flag bool) {
    fmt.Println(str, str2, num, money, flag)
}

func main() {
    exec(demo)
}
```

##### 

##### 三、类型定义和类型别名，不要弄混了

类型别名与类型定义不同之处在于，使用类型别名需要在别名和原类型之间加上赋值符号(=)；使用类型别名定义的类型与原类型等价，而使用类型定义出来的类型是一种新的类型。
 在[Golang 1.9 新特性-类型别名（Type Aliases）](https://hao.io/2018/01/golang-type-aliases/):

1.设计初衷
 类型别名的设计初衷是为了解决代码重构时，类型在包(package)之间转移时产生的问题（参考[ Codebase Refactoring (with help from Go) ](https://talks.golang.org/2016/refactor.article)）。

考虑如下情景:

> 项目中有一个叫`p1`的包，其中包含一个结构体`T1`。随着项目的进展`T1`变得越来越庞大。我们希望通过代码重构将`T1`抽取并放入到独立的包`p2`，同时不希望影响现有的其他代码。这种情况下以往的go语言的功能不能很好的满足此类需求。类型别名的引入可以为此类需求提供良好的支持。

首先我们可以将T1相关的代码抽取到包p2中：



```go
package p2
 
type T1 struct {
...
}
func (*T1) SomeFunc() {
...
}
```

之后在p1中放入T1的别名



```go
package p1
import "p2"

type T1 = p2.T1
```

通过这种操作我们可以在不影响现有其他代码的前提下将类型抽取到新的包当中。现有代码依旧可以通过导入p1来使用T1。而不需要立马切换到p2，可以进行逐步的迁移。此类重构发生不仅仅存在于上述场景还可能存在于以下场景：

- 优化命名：如早期Go版本中的io.ByteBuffer修改为bytes.Buffer。
- 减小依赖大小：如io.EOF曾经放在os.EOF，为了使用EOF必需导入整个os包。
- 解决循环依赖问题。

参考[Go语言 | Go 1.9 新特性 Type Alias详解](https://www.flysnow.org/2017/08/26/go-1-9-type-alias.html)
 type alias这个特性的主要目的是用于已经定义的类型，在package之间的移动时的兼容。比如我们有一个导出的类型flysnow.org/lib/T1，现在要迁移到另外一个package中, 比如flysnow.org/lib2/T1中。没有type alias的时候我们这么做，就会导致其他第三方引用旧的package路径的代码，都要统一修改，不然无法使用。

有了type alias就不一样了，类型T1的实现我们可以迁移到lib2下，同时我们在原来的lib下定义一个lib2下T1的别名，这样第三方的引用就可以不用修改，也可以正常使用，只需要兼容一段时间，再彻底的去掉旧的package里的类型兼容，这样就可以渐进式的重构我们的代码，而不是一刀切。

```rust
//package:flysnow.org/lib
type T1=lib2.T1
```

