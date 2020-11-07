### go语言中的结构体

---

- tag(标签)

  每个结构体字段在它的声明中可以被指定一个标签（tag）。从语法上讲，字段标签可以是任意字符串，它们是可选的，默认为空字符串。 但在实践中，它们应该被表示成用空格分隔的键值对形式，并且每个标签尽量使用直白字面形式（``...``）表示，而键值对中的值使用解释型字面形式（`"..."`）表示。 比如下例：

  ```go
  struct {
  	Title  string `json:"title" myfmt:"s1"`
  	Author string `json:"author,omitempty" myfmt:"s2"`
  	Pages  int    `json:"pages,omitempty" myfmt:"n1"`
  	X, Y   bool   `myfmt:"b1"`
  }
  ```

  每个字段标签的目的取决于具体应用。上面这个例子中的字段标签用来帮助`encoding/json`标准库包来将上面这个结构体类型的某个值编码成JSON数据或者从一份JSON数据解码到上面这个结构体类型的某个值中。

- Go的结构体不支持`union`

- 导出字段和非导出字段

  go语言中strut的属性是否可以导出，**是遵循大小写的原则的**，即：**首字母大写的被导出，首字母小写的不被导出**

  1. 如果struct名称首字母是小写的，这个struct不会被导出。连同它里面的字段也不会导出，即使有首字母大写的字段名。
  2. 如果struct名称首字母大写，则struct会被导出，但只会导出它内部首字母大写的字段，那些小写首字母的字段不会被导出。

  但在嵌套结构体中上述情况又会变得不一样：

  **如果struct嵌套了，那么即使被嵌套在内部的struct名称首字母小写，也能访问到它里面首字母大写的字段**。

  ```go
  type animal struct {
    name string
    Speak string
  }
  
  type Horse struct {
    animal //可以这样写吗
    sound string
  }
  ```

  Horse中嵌套的animal是小写字母开头的，但Horse是能被导出的，所以能在其它包中使用Horse struct，其他包也能访问到animal中的Speak属性。

- struct的反射

- 一个结构体类型不能（直接或者间接）含有一个类型为此结构类型的字段。

- 在go语言中国，语法形式`T{...}`被称为一个composite Literal(组合字面量形式)

  注意：组合字面量`T{...}`是一个类型确定值，它的类型为`T`

- 初始化结构体的方式

  ```go
  type test struct {
    testInt int
  }
  ```

  1. 使用`new`来进行创建，返回值为指针类型

     ```go
     var t *test = new(test)
     t.testInt = 1
     ```

  2. 使用`&T{...}`创建，结果为指针类型

     ```go
     var t2 *test = &T{1}
     ```

  3. 使用`T{...}`来创建，结果为value类型

     ```go
     var t3 *test = T{1}
     ```

     

- **结构体属于值类型**，因此使用函数修改其原始值的时候需要用指针

- 



---

参考资料：

[struct的导出与暴露问题](https://studygolang.com/articles/16436)