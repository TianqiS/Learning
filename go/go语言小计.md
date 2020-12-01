### go语言小计

---

- go语言没有while循环，唯一的循环就是`for`循环

- `for`循环

  go语言中的for循环有三种形式

  1. `for init; condition; post { }`

     和C语言中的`for`循环一样

  2. `for condition{}`

     和`while`一样

  3. `for {}`

     和`for(;;)`一样

- 函数声明和调用中遇到的问题

  如下代码所示：

  ```go
  func main() {
    //倘若想要在main函数中声明一个函数只能采用如下的形式
    test := func(node *TreeNode) {
      //....
    }
    //但若想要声明的函数递归调用自己应采用如下方式
    var test func(node *TreeNode)
    test = func(node *TreeNode) {
      //balabala
      test()
      //balabala
    }
  }
  ```

- 声明一个slice

  ```go
  result := []int //这种写法是不对的
  
  var result []int //OK
  ```

- go语言中的自增

  > *The increment statement i++ adds 1 to i ; it’s equivalent to i += 1 which is in turn equivalent to i = i + 1 . There’s a corresponding decrement statement i– that subtracts 1. These are statements, not expressions as the y are in most languages in the C family, so j = i++ is illegal, and the y are postfix only, so –i is not legal either.*

  在go语言中`i++`是语句而不是表达式，等价于`i += 1`因此`j=i++`这种写法是错误的

  同时，在go语言中没有`++i`和`--i`

- `assign entry to nil map`错误

  在下面语句中

  ```go
  var result map[int][]int
  
  result[1] = []int{1,2}
  ```

  会报出`assign entry to nil map`错误，这是因为没有对map进行初始化

  正确做法应该是

  ```go
  result := make(map[int][]int)
  ```

  > This variable m is a map of string keys to int values:
  > `var m map[string]int`
  > Map types are reference types, like pointers or slices, and so the value of m above is nil; it doesn't point to an initialized map. A nil map behaves like an empty map when reading, but attempts to write to a nil map will cause a runtime panic; don't do that. To initialize a map, use the built in make function:
  > `m = make(map[string]int)`

  同为引用类型的slice，在使用`append` 向nil slice追加新元素就可以，原因是`append`方法在底层为slice重新分配了相关数组让nil slice指向了具体的内存地址

  > nil map doesn't point to an initialized map. Assigning value won't reallocate point address.
  > The append function appends the elements x to the end of the slice s, If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

- 对Slice进行排序

  ```go
  sort.Slice(mySlice, func(i, j int) bool {
        return  mySlice[i].height < mySlice[j].height //如果i对应的元素比j要小
     })
  ```
  
- Slice

  slice是对数组的抽象，长度不固定，可在追加时使切片的容量增大
  
  定义slice的方法：
  
  1. `var test []int`
  2. `var slice1 []int = make([]int, len)`
  3. `slice1 := make([]int, len)`
  
  `make([]T, length, capacity)` 其中capacity是可选参数
  
- go语言交换元素
  
  `a, b = b, a`
  
- go语言操纵部分数组
  
  ```go
  func test(a []int) {
    balabala
  }
  
  test(nums[2:])
  ```
  
- go语言调用函数但却不需要函数返回值

  可以这样写

  ```go
  func test() int {
    ...
  }
  
  _ = test()
  ```

  ​    

- golang中没有三目运算符

- golang命令行build

  1. 无参数编译

     如果源码中没有依赖 GOPATH 的包引用，那么这些源码可以使用无参数 go build。格式如下：

     `go build`

     会build当前目录下的所有go文件

  2. 编译多个文件

     编译同目录的多个源码文件时，可以在 go build 的后面提供多个文件名，go build 会编译这些源码，输出可执行文件，“go build+文件列表”的格式如下：

     `go build file1.go file2.go……`

  3. 编译一个包

     “go build+包”在**设置 GOPATH 后**，可以直接根据包名进行编译，即便包内文件被增（加）删（除）也不影响编译指令。

     目录结构如下所示：

     ```text
     src
     	-main
     		-main.go
     		-cli.go
     ```

     在build的时候在哪个路径下build都可以，build命令

     `go build -o filename main`  
  
- 循环中的标签

  go语言可以使用`goto`语言在函数内进行跳跃，跳跃到任意位置

  如下所示：

  ```go
  for {
    if(true) {
      goto breakHere
    }
    
    breakHere:
    fmt.Println(123)
  }
  ```

  - Break + 标签

    ```go
    func main() {
    Loop: //则此跳转标签必须刚好声明在一个包含此break语句的可跳出流程控制代码块之前
    	for j:=0;j<3;j++{
    		fmt.Printf("j为 %d \n", j)
    		for a:=0;a<5;a++{
    			fmt.Println("a为" +strconv.Itoa(a))
    			if a>3{
    				break Loop
    			}
    		}
    	}
    }
    //在没有使用loop标签的时候break只是跳出了第一层for循环
    //使用标签后跳出到指定的标签,break只能跳出到之前，如果将Loop标签放在后边则会报错
    //break标签只能用于for循环，跳出后不再执行标签对应的for循环
    
    /*
    j为 0 
    a为0
    a为1
    a为2
    a为3
    a为4
    */
    ```

  - Continue + 标签

    ```go
    func main() {
    Loop: //则此跳转标签必须刚好声明在一个包含此continue语句的循环流程控制代码块之前
       for j:=0;j<3;j++{
          fmt.Printf("j为 %d \n", j)
          for a:=0;a<5;a++{
             fmt.Println("a为" +strconv.Itoa(a))
             if a>3{
                continue Loop
             }
          }
       }
    }
    //在没有使用loop标签的时候break只是跳出了第一层for循环
    //使用标签后跳出到指定的标签,break只能跳出到之前，如果将Loop标签放在后边则会报错
    //break标签只能用于for循环，跳出后不再执行标签对应的for循环
    
    /*
    j为 0 
    a为0
    a为1
    a为2
    a为3
    a为4
    j为 1 
    a为0
    a为1
    a为2
    a为3
    a为4
    j为 2 
    a为0
    a为1
    a为2
    a为3
    a为4
    */
    ```

- `...`

  这是go语言中的语法糖，可以将slice拆解开

  ```go
  func test1(args ...string) { //可以接受任意个string参数
      for _, v:= range args{
          fmt.Println(v)
      }
  }
  
  func main(){
  var strss= []string{
          "qwr",
          "234",
          "yui",
          "cvbc",
      }
      test1(strss...) //切片被打散传入
  }
  ```

  `[...]int {1,2,3}`

  表示该数组的长度由后面的初始化数据个数决定

- []byte和string的区别

- 声明数组时遇到的问题

  如果这样写会报错

  ```go
  i := 5
  var result [i]int //error
  ```

  应该这样写

  ```go
  i := 5
  result := make([]int, i)
  ```

- 结构体中的匿名变量

- Interface类型

  一种类型只要实现接口声明的方法就算实现了接口，同样一种类型能同时支持实现多个接口了，一个接口也能被多种类型实现。如果一种类型实现了某个接口，那么这种类型的对象能够赋值给这个接口类型的变量。

  最后的一部分解释一下 `interface{}` 类型，这种类型的接口没有声明任何一个方法，俗称空接口。因为任何类型的对象都默认实现了空接口（上文提到任意类型只要实现了接口的方法就算实现了对应的接口，由于空接口没有方法，故所有类型默认都实现了空接口）在需要任意类型变量的场景下非常有用。

  

  ```golang
  package main
  
  import (
      "fmt"
  )
  
  func PrintAll(vals []interface{}) {
      for _, val := range vals {
          fmt.Println(val)
      }
  }
  
  func main() {
      names := []string{"stanley", "david", "oscar"}
      vals := make([]interface{}, len(names))
      for i, v := range names {
          vals[i] = v
      }
      PrintAll(vals)
  }
  
  // stanley
  // david
  // oscar
  ```

  

- `v.(int)`的含义

  go语言中的断言

  
