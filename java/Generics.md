### Generics

---

- the reason to use generics

  - Generics add stability to your code by making more of your bugs detectable at compile time(**在编译时进行检查**)

    ![截屏2020-10-29 下午3.23.03](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68e8og0gj319q0fon0o.jpg)

  - Stronger type checks at compile time

  - Elimination of casts

    ![截屏2020-10-29 下午3.24.16](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68fgp898j30uo0fgtbm.jpg)

  - ![截屏2020-10-29 下午3.24.43](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68fxbek1j316405yt9t.jpg)

- Generic Type

  A generic class is defined with the folowing format:

  ```java
  class name<T1, T2, ..., Tn> {...}
  ```

  ![截屏2020-10-29 下午3.28.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68jnn8saj30qy0ko40s.jpg)

  - 不可以用基本类型

    ![截屏2020-10-29 下午3.28.49](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68k75y7tj316k068gmt.jpg)

  - ![截屏2020-10-29 下午3.28.59](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68kdlofoj311u022aad.jpg)

- Type paramter naming conventions

  ![截屏2020-10-29 下午3.29.55](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68lcqjepj316u0d60uk.jpg)

- Invoking and Instantiating a Generic Type

  ```java
  Box<Integer> integerBox; //注意是包装器类型
  ```

  you can think of a generic type invocation as being similar to an ordinary method invocation, but instead of passing an argument to a method, you are passing a type argument -Integer- to the Box class

  上述代码仅仅创建了一个一个reference，并没有new一个对象

  ```java
  Box<Integer> integerBox = new Box<Integer>();
  ```

- The diamond (钻石符号)

  在Java SE7和之后的版本，可以这样写

  ```java
  Box<Integer> integerBox = new Box<>();
  ```

  编译器会自动识别,<>被称为"钻石"

- Multiple Type Parameters

    ![截屏2020-10-29 下午3.38.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68uadeq2j30z00t6dj9.jpg)

  ![截屏2020-10-29 下午3.40.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gk68w5tzhvj31e80h2djp.jpg)
  
  这里实际上是有自动转型的
  
- parameterized Types

    可以这样:

    ```java
    OrderedPair<String, Box<Integer>> p = new OrderedPair<>("test", new Box<Integer>(...));
    ```

- Raw Types

    Actual type：

    ![截屏2020-10-29 下午3.44.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6901ds5fj31800783zq.jpg)

    If the actual type argument is omitted, you create a raw type of Box<T>:

    ```java
    Box rawBox = new Box();
    ```

    Therefore, Box is the raw type of the generic type Box<T>. **However, a non-generic class or interface type is not a raw type**

    为了反向兼容，raw Type是允许存在的

    ![截屏2020-10-29 下午3.47.22](https://tva1.sinaimg.cn/large/0081Kckwgy1gk693ifpv3j31am0oo79j.jpg)

    - 调用泛型方法在raw type里面会有警告

      ![截屏2020-10-29 下午3.48.50](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6953dul6j31620by76k.jpg)

      ![截屏2020-10-29 下午3.49.36](https://tva1.sinaimg.cn/large/0081Kckwgy1gk695v75z0j315k06g75e.jpg)

- Unchecked Error Message

    ![截屏2020-10-29 下午3.50.16](https://tva1.sinaimg.cn/large/0081Kckwgy1gk696jbxshj31920rydkp.jpg)

- Generic Methods

    - Static and non-static generic methods are allowed, as well as generic class constructors

    - ![截屏2020-10-29 下午3.53.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69a6xvjtj318o0nm42z.jpg)

      对于静态的泛型方法来说，the type parameter section must appear befor the method's return type  

        ![截屏2020-10-29 下午3.56.09](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69cnheijj314k0rujw0.jpg)

- invoke a Generic Method

    ![截屏2020-10-29 下午3.56.54](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69dfp3fij30wc06mmyh.jpg)

    类型的推断(type inference)

    ![截屏2020-10-29 下午3.57.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69ed2qk3j30to0663zn.jpg)

- Bounded Type parameters

    ![截屏2020-10-29 下午4.00.16](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69gy62mbj30xp0u042g.jpg)

- Multiple Bounds

    ```java
    <T extends B1 & B2 & B3>
    ```

    ![截屏2020-10-29 下午4.01.25](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69i61exvj317k0kegpo.jpg)

    需要把ClassA放在最前面

- Generic Methods and Bounded Type Parameters

    ![截屏2020-10-29 下午4.02.51](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69jlztgdj319e0nqwii.jpg)

    上面代码不OK，解决方法：

    ![截屏2020-10-29 下午4.04.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69kvhgpcj31bg0ps787.jpg)

    需要指定implements Comparable接口的T

- 泛型的向上转型

    ![截屏2020-10-29 下午4.06.05](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69mz62nkj30sk07gabc.jpg)

    但是对于下面这种情况就不OK

    ![截屏2020-10-29 下午4.07.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69os8mmcj31a80n0gqc.jpg)

    因为`Box<Integer>`并不是`Box<Number>`的子类

    ![截屏2020-10-29 下午4.10.01](https://tva1.sinaimg.cn/large/0081Kckwgy1gk69r2v50rj30ve0logy0.jpg)
    
- Wildcards(通配符)

    - In generic code, the question mark (?), called the wildcard, represents an unknown type.

    - The wildcard can be used in a variety of situations: as the type of a parameter, field, or local variable; sometimes as a return type (though it is better programming practice to be more specific).

    - **The wildcard is never used as a type argument for a generic method invocation, a generic class instance creation, or a supertype.**

      例如：

      ```java
      List<? extends A> list = new List<A>(); //后面实例化的时候填入的A，不可以是一个通配符的形式 
      ```

      

    例如：

    ```java
    List<? extends Number>
    ```

    - The term List<Number> is more restrictive than List<? extends Number> because the former matches a list of type Number only, whereas the latter matches a list of type Number or any of its subclasses.

- **Unbounded Wildcards**

    - The unbounded wildcard type is specified using the wildcard character (?), for example, List<?>. This is called a *list of unknown type*.

- **Lower Bounded Wildcards**

    ![截屏2020-10-30 上午10.01.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gk74q87jp1j310w0cmwft.jpg)

- Type Erasure

