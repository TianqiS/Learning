### Object

---

- method signature

  **Definition:** Two of the components of a method declaration comprise the *method signature*—the method's name and the parameter types.

  Example: calculateAnswer(double, int, double, double)

- Overloading

  ![截屏2020-11-25 下午6.58.08](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1mcc03tnj30ud0fcabj.jpg)

  The compiler does not consider return type when differentiating methods, so you cannot declare two methods with the same signature even if they have a different return type.

- 传入任意数量的参数

  ![截屏2020-11-25 下午7.39.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1njoulkuj30pa0a540m.jpg)

  ![截屏2020-11-25 下午7.40.20](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1nk8p2mej30p10bv0ut.jpg)

  The method can then be called with any number of that parameter, including none.

- 变量的shadowing

  A parameter can have the same name as one of the class's fields. If this is the case, the parameter is said to *shadow* the field.

- passing primitive data type arguments

  ![截屏2020-11-25 下午7.43.54](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1nnyx9poj30ot07egmt.jpg)

  **Reference data type parameters, such as objects, are also passed into methods *by value*.**

  ![截屏2020-11-25 下午7.45.06](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1np75fl0j30py058jsb.jpg)

  ![截屏2020-11-25 下午7.46.44](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1nqyrvq8j30qc0fn0vk.jpg)

  ![截屏2020-11-25 下午7.47.04](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1nr95wvxj30q70ffn00.jpg)

- creating objects

  ![截屏2020-11-25 下午7.49.03](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1ntakqdtj30ou08u0th.jpg)

- new operator

  The new operator instantiates a class by allocating memory for a new object and returning a reference to that memory.

- **Initializing an Object**

  This default constructor calls the class parent's no-argument constructor,

  or the Object constructor if the class has no other parent.

  If the parent has no constructor (Object does have one), the compiler will reject the program.

- calling an object's methods

  Remember, invoking a method on a particular object is the same as sending a message to that object.

- ![截屏2020-11-25 下午8.05.53](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1oavzkgxj30ni0elac2.jpg)

- this keyword

  ![截屏2020-11-25 下午8.07.52](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1ocxfk0aj30m706vq3z.jpg)

  ![截屏2020-11-25 下午8.08.58](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1oe0xxw2j30m50eggnd.jpg)

- ![截屏2020-11-25 下午8.10.26](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1ofmfciuj30ml07w75o.jpg)

- For members, there are two additional access modfiers: private and protected.

  ![截屏2020-11-25 下午8.15.26](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1okx5po4j30n505wjsb.jpg)

- The protected modifier specifies that the member can only be accessed within its own package (as with *package-private*) and, in addition, by a subclass of its class in another package.

- ![截屏2020-11-25 下午8.25.39](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1ovg1gv7j30fe06nmxo.jpg)

- Static

  ![截屏2020-11-25 下午8.29.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1oz3dginj30hy056js8.jpg)

- ![截屏2020-11-25 下午8.30.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1p0g5hkoj30kh0avdhi.jpg)

- constant

  static + final

  ![截屏2020-11-25 下午8.31.56](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1p1y097oj30lc0ccgnn.jpg)

- static initialization blocks

  ![截屏2020-11-25 下午8.34.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1p4pnl2ij30gt08ct9j.jpg)

  ![截屏2020-11-25 下午8.35.10](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1p5bjcloj30kz0940u5.jpg)

- initializing instance members

  ![截屏2020-11-25 下午8.36.55](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1p74k0m0j30kd0afdhi.jpg)

- nested classes

  - static ---static nested classes
  - Non-static --- inner classes

  ![截屏2020-11-25 下午8.42.24](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1pcsyq2dj308k05adfw.jpg)

  ![截屏2020-11-25 下午8.43.12](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1pdnxioij30kf07k0uc.jpg)

- static nested classes

  ![截屏2020-11-25 下午9.03.46](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1pz36x5zj30vg0guwgt.jpg)

- inner classes

  ![截屏2020-11-25 下午9.04.40](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1q005ys4j31400dcmzi.jpg)

  An instance of InnerClass can exist only within an instance of OuterClass and has direct access to the methods and fields of its enclosing instance.

  ![截屏2020-11-25 下午9.05.25](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1q0skkl1j313u06agmo.jpg)

- local classes

  Local classes are classes that are defined in a *block*, which is a group of zero or more statements between balanced braces. You typically find local classes defined in the body of a method.

  在方法内定义的classes, local classes只能获取final或者effectively final 的local variables and parameters

  ![截屏2020-11-25 下午9.09.41](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1q56pyihj31400k00ww.jpg)

    Local classes不是静态的，并且和inner classes类似![截屏2020-11-25 下午9.12.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1q836futj314k0nyq7g.jpg)

  可以拥有静态成员

  ![截屏2020-11-25 下午9.14.01](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1q9s0w18j313m0omgr8.jpg)

- anonymous classes

  ![截屏2020-11-25 下午9.14.45](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qagkzfuj312q06mjso.jpg)

  **Anonymous classes are expressions**

  ![截屏2020-11-25 下午9.16.34](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qccne2yj310c05gaau.jpg)

  记得要有; 因为定义anonymous classes 是expression

  ![截屏2020-11-25 下午9.19.17](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qf6r8p4j31440o0gpa.jpg)

  ![截屏2020-11-25 下午9.23.26 2](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qk2fgw6j313q0k4tc4.jpg)

  在匿名类中不能定义constructors

- enum types

  An *enum type* is a special data type that enables for a variable to be a set of predefined constants.

  由于是predefined constants，所以大写

  ![截屏2020-11-25 下午9.27.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qnt9m0ej30ve03uwf1.jpg)

  ![截屏2020-11-25 下午9.29.14](https://tva1.sinaimg.cn/large/0081Kckwgy1gl1qpk0d9ej315q0kcwia.jpg)

  
