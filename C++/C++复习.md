### C++复习

- 引用

  引用变量是一个**别名**，也就是说，它是某个已存在变量的另一个名字。一旦把引用初始化为某个变量，就可以使用该引用名称或变量名称来指向变量。

  引用和指针的不同之处：

  - **不存在空引用**。引用必须连接到一块合法的内存。
  - 一旦引用被初始化为一个对象，就**不能被指向到另一个对象**。指针可以在任何时候指向到另一个对象。
  - 引用**必须在创建时被初始化**。指针可以在任何时间被初始化。

- 把引用作为函数参数

  ```c++
  void swap(int& x, int& y);
  ```

- 把引用作为函数返回值

  ```c++
  double& setValues(int i) {
    return vals[i];
  }
  ```

  **注意：**当返回一个引用时，要注意被引用的对象不能超出作用域。所以返回一个对局部变量的引用是不合法的，但是，可以返回一个对静态变量的引用。

  ```c++
  int& func() {
    int q;
    //! return q; //在编译时发生错误
    static int x;
    return x;
  }
  ```

- 函数后面的`const`关键字

  给隐含的this指针加const，表示这个this指向的东西是const的，也就是说这个函数中无法改动数据成员了。const是一种保证，告诉你这个成员不会改变对象的状态。

  声明一个成员函数的时候用const关键字是用来说明这个函数是 “只读(read-only)”函数，也就是说明这个函数不会修改任何数据成员(object)。 为了声明一个const成员函数， 把const关键字放在函数括号的后面。声明和定义的时候都应该放const关键字。

  任何不会修改数据成员的函数都应该声明为const类型。如果在编写const成员函数时，不慎修改了数据成员，或者调用了其它非const成员函数，编译器将指出错误，这无疑会提高程序的健壮性。

  ```c++
  #include<iostream>
  using namespace std;
  class temp
  {
  public:
      temp(int age);
      int getAge() const;
      void setNum(int num);
  private:
      int age;
  };
  
  temp::temp(int age)
  {
      this->age = age;
  }
  
  int temp::getAge() const
  {
      age+=10; // #Error...error C2166: l-value specifies const object #
      return age;
  }
  
  void main()
  {
      temp a(22);
      cout << "age= " << a.getAge() << endl;
  }
  ```

  因为声明了const函数，所以不可以修改任何数据成员，但是在这里给age数据成员加了10， 所以产生错误。

- C++创建类的时候`new`与不`new`的区别

  在C++中创建对象有两种方式：

  1. `Test test;`
  2. `Test* pTest = new Test();`

  上述两种方法都阔以实例化一个对象，但是这两种方法有很大的区别，区别于**对象所在的内存空间不同**

  内存的分配的方式有下面三种形式：

  1. 从静态存储区域分配。内存在程序编译的时候就已经分配好，这块内存在程序的整个运行期间都存在。例如全局变量，static 变量。
  2. 在栈上创建。在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束后在将这些局部变量的内存空间回收。在栈上分配内存空间效率很高，但是分配的内存容量有限。
  3. 从堆上分配的。程序在运行的时候用 malloc 或 new 申请任意多少的内存，程序员自己负责在何时用 free 或 delete 释放内存。

  不使用`new`创建对象时，对象的内存空间是在栈中的，其作用的范围知识在函数内部，函数执行完后会调用析构函数，删除该对象

  而使用`new`创建对象是在堆中进行创建的，必须要程序员手动的去管理该对象的内存空间

- 疑问

  ```c++
  char * string_pointer;
  string_pointer = "asdasadfdfas";
  sizeof(string_pointer); // 会打印出8，这是因为在64位系统中指针的大小就是8个字节
  
  char test[1024] = "test";
  cout << sizeof(test) << endl; //这里会打印出数组的大小1024
  ```

  拓展

  ```c++
  char* string_pointer = "test";
  printf("pointer is %s", string_pointer); //test
  printf("pointer is %p", string_pointer); //会打印指针的地址
  ```

  

- gcc和g++

- 关于C++中`*ptr++`的问题

  `*ptr++`相当于

  ```c++
  *ptr;
  ptr++; //并不会增加ptr指向的值的value
  ```

  如果想增加ptr指向的值的value，采用如下形式

  ```c++
  (*ptr)++;
  *ptr += 1;
  ```

  下列代码会输出:ab

  ```c++
  char *test = "abcdefg";
  std::cout << *test++ << *test <<std::endl;
  ```

- C++中的`__CHAR_BIT__`

  `CHAR_BIT` is the number of bits in `char`. These days, almost all architectures use 8 bits per byte but it is not the case always. Some older machines used to have 7-bit byte.

- c语言中的赋值语句的返回值[原文](https://blog.csdn.net/wu_nan_nan/article/details/70162362)

  赋值语句返回值是左值的引用

  ![这里写图片描述](https://img-blog.csdn.net/20170413213454939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VfbmFuX25hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

  
