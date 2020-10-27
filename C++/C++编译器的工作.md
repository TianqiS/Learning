### C++编译器的工作

c++编译器唯一要做的就是把文本变为中继格式-obj，之后object会传入linker，linker会做所有linking的事情

在compiler产出这些object时，会做好几件事：

1. 首先需要进行pre-process代码，所有的preprocessor语句会在那时被评估
2. 预处理之后，会进入tokenizing(标记解释)和parsing(解析)阶段，简单来讲就是将C++的文本处理成编译器能懂和处理的语言，基本上的结果就是创建某种叫做abstract syntax tree(抽象语法树)，也就是我们代码的表达，但是是以抽象语法树的形式

说到底编译器的工作也就是把代码转化成要么是`constant data`要么是`instructions`，当编译器创建了这棵抽象语法树之后，就可以开始产生代码了，这个代码是真正的cpu会执行的机器码

在C++里面根本不需要关心文件，不像java里面每个类需要和每个文件一一对应，但是在C++里面不存在文件这种东西，文件只是用来给编译器提供源码的某种方法，你需要告诉编译器文件类型和编译器该如何处理它，比如说新建一个`.cpp`文件，编译器就会把它当做C++文件；建一个`.c`文件，编译器就会把它当做C文件

- 常见的preprocessor语句：

  - #include
  - #define
  - #if
  - #ifdef

- constant folding

  任何常量都能在编译时计算出来，例如：
  
  ```c++
  int mu() {
    return 2 *5;
  }
  ```
  
  对编译器来说就是`return 10;`
---

### How the C++ linker works

在编译阶段发生的错误以C开头，例如`error C2143`

在链接阶段发生的错误以LNK开头，例如`LNK1561`

在build+link的时候，需要指明一个entry point，否则会出现链接阶段的报错，entry point不一定是一个main函数，可以自己指定为其他函数

`#include`实际上就是将#include那里的代码替换为需要引入的内容，因此，#include使用不当可能会发生链接错误。

例如：

```c++
//log.h
void Log(const char* message) {
  std::cout << message << std::endl;
}
```

```c++
//log.cpp
#include "log.h"

void InitLog() {
  Log("Initialized Log");
}
```

```c++
// main.cpp
#include "log.h"
static int Multiply(int a, int b) { //这里使用static的原因是有讲究的
  Log("multiply");
  return a * b;
}
```

上述代码会报链接错误，这是因为链接器并不知道自己要链接哪一个程序，因为在`log.cpp`和`main.cpp`里同时引入了`#include "log.h"`，因此会报错

解决上述问题有三种办法：

1. `static void Log(const char* message) {...}`，将函数静态化，这意味着对这个log函数链接时只应该发生在该文件内部，也就是说，当这个log函数被include在log.cpp中和main.cpp时，只会对该文件内部有效

2. `inline void Log(...) {...}`使用inline关键字

3.   将函数声明放在.h文件中

   ```c++
   //log.h
   
   void Log(const char* message);
   ```

   ```c++
   // log.cpp
   #include "Log.h"
   
   void InitLog() {
     Log("Initialized Log");
   }
   
   void Log(const char* message) {
     std::cout << message << std::endl;
   }
   ```

   ```c++
   // main.cpp
   #include "log.h"
   static int Multiply(int a, int b) {
     Log("multiply");
     return a * b;
   }
   ```

对于链接器来说，函数签名的判定标准是：

- 函数的返回类型
- 函数的参数

  



  

