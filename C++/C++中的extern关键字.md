### C++中的extern关键字

---

> basically, the `extern` keyword extends the visibility of the C variables and C functions.

首先需要明白两个概念：

1. Declaration

   **Declaration** of a variable or function simply declares that the variable or function exists somewhere in the program, but the memory is not allocated for them. The declaration of a variable or function serves an important role–it tells the program what its type is going to be. In case of *function* declarations, it also tells the program the arguments, their data types, the order of those arguments, and the return type of the function. So that’s all about the declaration.

2. Definition

   **definition**, when we *define* a variable or function, in addition to everything that a declaration does, it also allocates memory for that variable or function. Therefore, we can think of definition as a superset of the declaration (or declaration as a subset of definition).

一个变量或者函数可以被declared很多次，但是只能defined一次

the `extern` turns out that when a function is declared or defined, the `extern` keyword is **implicitly assumed**

Such as:

```c++
int foo(int arg1, int arg2);
```

the complier treats it as:

```c++
extern int foo(int arg1, char arg2);
```

Since the `extern` keyword extends the function’s visibility to the whole program, the function can be used (called) anywhere in any of the files of the whole program, provided those files contain a declaration of the function. (With the declaration of the function in place, the compiler knows the definition of the function exists somewhere else and it goes ahead and compiles the file). So that’s all about `extern` and functions.

上面的讨论是对function的讨论，接下来讨论`extern`对于variables的情况

定义变量的时候就和函数不一样了，定义变量的时候不可以省略掉`extern`关键字

> we saw that the `extern` keyword was present implicitly. But this isn’t the case with variables. If it were, memory would never be allocated for them. Therefore, we need to include the `extern` keyword explicitly when we want to declare variables without defining them. 

```c++
extern int var;
```

下面情况会报错：

```c++
extern int var;
int main(void) {
  var = 10; //这是因为var虽然被声明但是没有被分配任何内存空间
  return 0;
}
```

值得注意的是，下面的情况却会编译成功：

```c++
extern int var = 0;
int main(void) { 
 var = 10; 
 return 0; 
} 
```

这是因为：if a variable is only declared and an initializer is also provided with that declaration, then the memory for that variable will be allocated–in other words, that variable will be considered as defined. Therefore, as per the C standard, this program will compile successfully and work.



总结：

1. A declaration can be done any number of times but definition only once.
2. the `extern` keyword is used to extend the visibility of variables/functions.
3. Since functions are visible throughout the program by default, the use of `extern` is not needed in function declarations or definitions. Its use is implicit.
4. When `extern` is used with a variable, it’s only declared, not defined.
5. As an exception, when an `extern` variable is declared with initialization, it is taken as the definition of the variable as well.

   