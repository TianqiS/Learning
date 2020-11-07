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

  

