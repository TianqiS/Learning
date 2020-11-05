### Numbers and Strings

---

Java platform provides *wrapper* classes for each of the primitive data types.These classes "wrap" the primitive in an object.

Often, the wrapping is done by the compiler—if you use a primitive where an object is expected, the compiler *boxes* the primitive in its wrapper class for you.Similarly, if you use a number object when a primitive is expected, the compiler *unboxes* the object for you.

- 使用Number oject的几个原因

  ![截屏2020-10-23 上午8.08.51](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyy4m8li2j313y0diq5e.jpg)

- Methods Implemented by all Subclasses of Number

  ![截屏2020-10-23 上午8.10.24](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyy6g9s7gj313k0gwq5t.jpg)

### Beyond Basic Arithmetic

---

The Math class in the java.lang package provides methods and constants for doing more advanced mathematical computation.

for example, like this:

`Math.cos(angle);`

- The Math class includes two constants:

  ![截屏2020-10-23 上午8.29.23](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyypyfmiyj30z405y3zi.jpg)

- Basic Math Methods

  ![截屏2020-10-23 上午8.32.38](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyytdfzzjj31520s6wkp.jpg)

- Expoeential and Logarithmic Methods

  ![截屏2020-10-23 上午8.33.44](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyyugqtv7j31340bu775.jpg)

- Trigonometric Methods

  ![截屏2020-10-23 上午8.34.17](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyyv1moxjj312q0newjq.jpg)

- `Math.random()`

---

### Characters

java中也提供了对char的包装类Character object

You can create a Character object with the Character constructor:

`Character ch = new Character('a');`

- The Character class is immutable, so that once it is created, a Character object cannot be changed

- Useful Methods in the Character Class

  ![截屏2020-10-23 上午8.58.34](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyzkcpfvsj313w0mwtd7.jpg)

---

### Strings

- In Java, strings are objects

- 声明strings的方法

  ![截屏2020-10-23 上午9.01.11](https://tva1.sinaimg.cn/large/0081Kckwgy1gjyznjhge5j313s0iwtbz.jpg)

- **The String class is immutable**, so that once it is created a String object cannot be changed

- string的常用方法

  - `length()`
  - `getChars()`
  - `str1.concat(str2)`

- Converting Strings to Numbers

  ![截屏2020-10-23 上午9.17.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0448ujhj311y060t9w.jpg)

  例如：

  ` Float.valueOf("1.5")`

  但上面代码返回的是Float类型的object，因此需要转换为基本类型

  `float a = (Float.valueOf("1.5")).floatValue();`

  除上述方法之外，还有返回primitive类型的方法

  `float a = Float.parseFloat("1.5")`

- Converting Numbers to Strings

  - `String s1 = "" + 1`

  - `String s2 = String.valueOf(i)`

  - Each of the Number subclasses includes a class method, toString()

    `String s3 = Integer.toString(i)`

- Strings的其他方法

  ![截屏2020-10-23 上午9.32.15](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0jdxafpj314q0geadd.jpg)

  ![截屏2020-10-23 上午9.32.26](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0jl8ml1j313g0n0gqk.jpg)

  ![截屏2020-10-23 上午9.33.14](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0kfq83ij311l0u0n4x.jpg)

  ![截屏2020-10-23 上午9.33.30](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0koi0akj312y0u0ags.jpg)

  ![截屏2020-10-23 上午9.34.22](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0lmav2cj316j0u0gsm.jpg)

  ![截屏2020-10-23 上午9.35.58](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz0naed87j314b0u0wlh.jpg)

---

### The StringBuilder Class

StringBuilder objects are like String objects, except that they can be modified.

Internally, these objects are treated like variable-length arrays that contain a sequence of characters.

StringBuilder可以被更改

**Strings should always be used unless string builders offer an advantage in terms of simpler or better performance.**

> For example, if you need to concatenate a large number of strings, appending to a StringBuilder object is more efficient.

- Length and Capacity

  ![截屏2020-10-23 上午9.58.41](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1aw8g2ej30wq0cymzk.jpg)

- String Builder Constructors

  ![截屏2020-10-23 上午9.59.44](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1bzcsj2j30ws0fkq6h.jpg)

- Length and Capacity Methods

  ![截屏2020-10-23 上午10.01.21](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1dn9t4xj30ww0bkjto.jpg)

  当向string builder里面添加元素的时候，如果满了，capacity会自动扩容

- various StringBuilder Methods

  ![截屏2020-10-23 上午10.04.35](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1h31rx9j30x60f00wb.jpg)

  ![截屏2020-10-23 上午10.04.57](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1hffhitj30vc0c2gnc.jpg)

  ![截屏2020-10-23 上午10.06.00](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1ihr34gj30x00jaaei.jpg)

  ![截屏2020-10-23 上午10.06.17](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1irjcblj30y80ayac7.jpg)

  可以将String builder和String相互之间进行转化，转化的目的是使用对应object的方法

---

### StringBuffer

There is also a StringBuffer class that is exactly the same as the StringBuilder class, except that it is thread-safe by virtue of having its methods synchronized.

和StringBuilder差不多，不过这个是线程安全的并且方法都是同步的

---

### Autoboxing and Unboxing

- boxing

  ![截屏2020-10-23 上午10.12.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1p7scpjj30wg0bq0ul.jpg)

  java编译器进行autoboxing的时机

  ![截屏2020-10-23 上午10.14.39](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1rilbxkj30ta05gmxu.jpg)

- Unboxing

  java应用unboxing的时机

  ![截屏2020-10-23 上午10.19.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gjz1wk5e4aj30s60500tl.jpg)