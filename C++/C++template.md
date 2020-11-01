### The Templates in C++

---

#### Function templates

Declaration: 

> Function templates are special functions that can operate with *generic types*. This allows us to create a function template whose functionality can be adapted to more than one type or class without repeating the entire code for each type.

the format for declaring funciton templates with type parameters is like this:

```c++
template <class identifier> function_declaration;

template <typename identifier> function_declaration;
```

例如创建一个比较大小的函数模板如下所示:

```c++
template <class myType>
myType getMax(myType a, myType b) {
	return (a > b? a : b);  
}
```

使用上述函数的方法如下所示：

`function_name <type> (parameters)`

```c++
int x, y;
GetMax<int>(x, y);
```

编译器处理模板函数的流程：

> When the compiler encounters this call to a template function, it uses the template to automatically generate a function replacing each appearance of `myType` by the type passed as the actual template parameter (`int` in this case) and then calls it. This process is automatically performed by the compiler and is invisible to the programmer.

会自动替换函数模板中出现的`myType`

另外一种情况：

```c++
int x, y;
GetMax(x, y);//这里没有传入<int>，但是编译器会自动识别
```

**注意：**Because our template function includes only one template parameter (`class T`) and the function template itself accepts two parameters, both of this `T` type, we cannot call our function template with two objects of different types as arguments:

```c++
int i;
long l;
k = GetMax(i, l); //这种情况是不OK的，因为函数模板定义的时候只有一种类型，但是传入的参数却有2种类型
```

当然也可以传入多个type parameter

```c++
template<class T, class U>
  T GetMin(T a, U b) {
  return ( a < b? a : b);
}
```

In this case, our function template `GetMin()` accepts two parameters of different types and **returns an object of the same type as the first parameter (`T`) that is passed.** For example, after that declaration we could call `GetMin()` with:

```c++
int i, j;
long l;
i = GetMin<int, long>(j, l);

//或者下面的形式
i = GetMin(j, l);
```

even though `j` and `l` have different types, since the compiler can determine the appropriate instantiation anyway.

---

#### Class templates

