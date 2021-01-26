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

#### 函数声明和调用中遇到的问题

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

#### 声明一个slice

  ```go
  result := []int //这种写法是不对的
  
  var result []int //OK
  ```

#### go语言中的自增

  > *The increment statement i++ adds 1 to i ; it’s equivalent to i += 1 which is in turn equivalent to i = i + 1 . There’s a corresponding decrement statement i– that subtracts 1. These are statements, not expressions as the y are in most languages in the C family, so j = i++ is illegal, and the y are postfix only, so –i is not legal either.*

  在go语言中`i++`是语句而不是表达式，等价于`i += 1`因此`j=i++`这种写法是错误的

  同时，在go语言中没有`++i`和`--i`

#### `assign entry to nil map`错误

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

#### 对Slice进行排序

  ```go
  sort.Slice(mySlice, func(i, j int) bool {
        return  mySlice[i].height < mySlice[j].height //如果i对应的元素比j要小
     })
  ```

#### Slice

  slice是对数组的抽象，长度不固定，可在追加时使切片的容量增大

  定义slice的方法：

  1. `var test []int`
  2. `var slice1 []int = make([]int, len)`
  3. `slice1 := make([]int, len)`

  `make([]T, length, capacity)` 其中capacity是可选参数

#### go语言交换元素

  `a, b = b, a`

#### go语言操纵部分数组

  ```go
  func test(a []int) {
    balabala
  }
  
  test(nums[2:])
  ```

#### go语言调用函数但却不需要函数返回值

  可以这样写

  ```go
  func test() int {
    ...
  }
  
  _ = test()
  ```

  ​    

#### golang中没有三目运算符

#### golang命令行build

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

#### 循环中的标签

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

#### Break + 标签

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

#### Continue + 标签

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

#### []byte和string的区别

#### 声明数组时遇到的问题

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

#### 结构体中的匿名变量

#### Interface类型

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

  

#### `v.(int)`的含义

  go语言中的断言

#### 没有参数的return语句

  ```go
  func test() (x, y int) {
    ...
    return
  }
  ```

  上述代码会直接return x和y的值

#### go语言指针default value is nil

  deference a nil pointer will lead to panic

#### 数组作为参数的时候传参

  ```go
  func test(a [][]int) {
      for _, i := range a {
          println(i)
      }
  }
  
  func main() {
      var b [3][2]int
      test(b) //会报错
  
  ```

  正确写法：

  ```go
  func test(a [3][2]int) {
      for _, i := range a {
          println(i)
      }
  }
  
  func main() {
      var b [3][2]int
      test(b)
  }
  ```

#### 创建动态数组

  ```go
  
  func createArray2(size int) []int{
  	return make([]int, size)
  }
   
  //错误 "non-constant array bound"
  func createArray(size int) []int{
  	return [size]int
  }
   
  func main(){
  	createArray(5)
  
  ```

  而在java中却不一样

  ```java
  
  public class Test {
      //正确
  	private static int[] createArray(int size){
  		return new int[size];
  	}
  	public static void main(String[] args) {
  		int[] a = createArray(5);
  		System.out.println(a[4]);
  	}
  }
  ```

#### 创建二维数组

  ```go
   var dp [][]int
      dp = make([][]int, m + 1)
      for i := 0; i < m + 1; i++ {
          dp[i] = make([]int, n + 1)
      }
  ```

#### 判断一个key是否在map中

  ```go
  if _, ok := map[key]; ok {
    
  }
  //另外golang也没有提供item是否在array当中的判断方法,如果程序里面频繁用到了这种判断,可以将array转化为以array当中的成员为key的map再用上面的方法进行判断,这样会提高判断的效率.
  ```

#### go语言中if的判断条件可以执行初始化

  golang对于if还有特殊的支持，golang支持在if条件当中加上初始化信息。

  

  比如：

  ```go
  if v := sample(); v < 10 {
      fmt.Println(v)
  }
  ```

  上面当中的v是**在if执行的时候才进行的初始化**，也就是说我们将变量的初始化和if判断结合在了一起。这个用法非常重要，在golang当中也大规模使用，所以我们一定要学会这个用法。

#### 比较方便的排序写法

  ```go
  s := []byte(str)
  sort.Slice(s, func(i, j int) bool { return s[i] < s[j] })
  ```

#### go语言判断字符串是否相等

  `"go" == "go" //true`

  `"go" == "Go" //false`

#### 字符串分割

  ```go
  s = strings.Split("abc,abc", ",")
  fmt.Println(s, len(s))
  ```

#### map的声明方法

  ```go
  word2ch := map[string]byte{}
  word1ch := make(map[string]byte)
  ```

#### 参数传递的方式

- 值传递

  事实证明 Golang 的参数传递（目前我接触的常用的类型如： string 、 int 、 bool 、array 、 slice 、 map 、 chan ）都是值传递

  **关于slice的参数传递(值传递)**

  使用数组元素(array[x])或者数组(array)作为函数参数时，其使用方法和普通变量相同。即是`值传递`

  ```go
  func modifyElem(a int) {
      a += 100
  }
  
  func modifyArray(a [5]int) {
      a = [5]int{5,5,5,5,5}
  }
  
  func main() {
      var s = [5]int{1, 2, 3, 4, 5}
      modifyElem(s[0])
      fmt.Println(s[0])
      modifyArray(s)
      fmt.Println(s)
  }
  
  // Output:
  // 1
  // [1 2 3 4 5]
  ```

#### channel 初始化

channel的初始化和map和slice数据类型一样，需要先创建再使用

```go
ch := make(chan int)
```

#### panic

> you should not attempt to recover from another package’s panic.

#### 实现set

go语言原生并没有set这一数据类型，但是我们可以通过map来实现一个set，以下是简易实现：

```go
set := make(map[string]bool) // New empty set
set["Foo"] = true            // Add
for k := range set {         // Loop
    fmt.Println(k)
}
delete(set, "Foo")    // Delete
size := len(set)      // Size
exists := set["Foo"]  // Membership

```

通过创建 map[string]bool 来存储 string 的集合，比较容易理解。但这里还有个问题，map 的 value 是布尔类型，这会导致 set 多占一定内存空间，而 set 不该有这个问题。

怎么解决这个问题？

设置 value 为空结构体，在 Go 中，空结构体不占任何内存。当然，如果不确定，也可以来证明下这个结论。

```go
unsafe.Sizeof(struct{}{}) // 结果为 0
```

优化过后的代码

```go
type void struct{}
var member void

set := make(map[string]void) // New empty set
set["Foo"] = struct{}{}         // Add
for k := range set {         // Loop
    fmt.Println(k)
}
delete(set, "Foo")      // Delete
size := len(set)        // Size
_, exists := set["Foo"] // Membership
```

#### 求幂操作

```go
/*
*    递归法 求x^n
 */
func powerf2(x float64, n int) float64 {
    if n == 0 {
        return 1
    } else {
        return x * powerf2(x, n-1)
    }
}

/*
*    循环法 求x^n
 */

func powerf3(x float64, n int) float64 {
    ans := 1.0

    for n != 0 {
        ans *= x
        n--
    }
    return ans
}
```

#### 获取int型长度

转字符串，再获取

```go
len(strcov.Itoa(a))
```

#### 获取int型变量某一位的值

```go
func getPo(num, pos int) int {
   numStr := strconv.Itoa(num)

   return int(numStr[pos] - 48)
}
```

#### 使用make创建切片

```go
test := make([]int , 5) //这一行代码会创建一个初始长度为5的切片
test := make([], 5, 6) //这一行代码会创建一个初始长度为5但是capacity为6的切片
```

#### 关于for range变量赋值问题

```go
test := []int{2, 3}

for _, j := range test {
  j = 2
}
//经过上述代码之后原来的slice中的值依旧是{2, 3}，这是因为for range的时候是值传递，因此直接修改j没用
//正确的修改方式

for i := range test {
  test[i] = 2
}
```





