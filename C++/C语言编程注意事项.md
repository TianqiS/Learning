### C注意事项

- 在C语言中`#include<>`和`#include ""`的区别

  查找位置不同，**一般情况下，#include <>在标准库头文件所在目录；#include ""在当前源文件所在目录下查找**

- `.c`文件和`.h`文件的区别

  `.h`文件只做声明，编译后不产生代码，函数定义要放在`.c`文件中，这样做的目的是为了实现软件的模块化，使软件结构清晰

- `void`指针的使用规则

  `void`指针可以指向任意类型的数据，就是说可以用任意类型的指针对`void`指针和对`void`指针赋值，如下所示：

  ```c
  int *a;
  void *p;
  p = a;
  ```

  如果要将 void 指针 p 赋给其他类型的指针，则需要强制类型转换，就本例而言：**a=（int \*）p**。在内存的分配中我们可以见到 void 指针使用：内存分配函数 malloc 函数返回的指针就是 **void \*** 型，用户在使用这个指针的时候，要进行强制类型转换，也就是显式说明该指针指向的内存中是存放的什么类型的数据 **(int \*)malloc(1024)** 表示强制规定 malloc 返回的 void* 指针指向的内存中存放的是一个个的 int 型数据。

  众所周知，如果指针 p1 和 p2 的类型相同，那么我们可以直接在 p1 和 p2 间互相赋值；如果 p1 和 p2 指向不同的数据类型，则必须使用强制类型转换运算符把赋值运算符右边的指针类型转换为左边指针的类型。

  ```c
  float *p1;
  int *p2;
  p1 = p2;
  //其中p1 = p2语句会编译出错，
  //提示“'=' : cannot convert from 'int *' to 'float *'”，必须改为：
  p1 = (float *)p2;
  ```

  而 **void \*** 则不同，任何类型的指针都可以直接赋值给它，无需进行强制类型转换。

  ```c
  void *p1;
  int *p2;
  p1 = p2;
  ```

  但这并不意味着，**void \*** 也可以无需强制类型转换地赋给其它类型的指针。因为"无类型"可以包容"有类型"，而"有类型"则不能包容"无类型"。

- 数据耐力问题

  ![截屏2020-10-25 下午10.21.15](https://tva1.sinaimg.cn/large/0081Kckwgy1gk1y08b6xxj312y0f47gx.jpg)

- `main`函数中的参数问题

  通常我们在写主函数时都是`void main()`或`int main() {..return 0;}`,但ANSI-C(美国国家标准协会,C的第一个标准ANSI发布)在C89/C99中main()函数主要形式为:

  1. `int main(void)`
  2. `int main(int argc,char *argv[]) = int main(int argc,char **argv)`

  其参数argc和argv用于运行时,把命令行参数传入主程序.其中ARG是指arguments,即参数.具体含义如下:

  1. `int argc`:英文名为arguments count(参数计数)

     count of cmd line args,运行程序传送给main函数的命令行参数总个数,包括可执行程序名,其中当argc=1时表示只有一个程序名称,此时存储在argv[0]中.

  2. `char **argv`:英文名为arguments value/vector(参数值)

     pointer to table of cmd line args,字符串数组,用来存放指向字符串参数的指针数组,每个元素指向一个参数,空格分隔参数,其长度为argc.数组下标从0开始,argv[argc]=NULL.

  **argv[0] 指向程序运行时的全路径名**

  **argv[1] 指向程序在DOS命令中执行程序名后的第一个字符串**

  

  

