### Exception

- 定义

  ![截屏2020-10-29 下午2.01.30](https://tva1.sinaimg.cn/large/0081Kckwgy1gk661e0it5j31ai058wfi.jpg)

- Call stack

  ![截屏2020-10-29 下午2.01.45](https://tva1.sinaimg.cn/large/0081Kckwgy1gk661lgbvpj31160n6142.jpg)

   ![截屏2020-10-29 下午2.03.15](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6636cgxrj319k0oiq7v.jpg)

  ![截屏2020-10-29 下午2.03.55](https://tva1.sinaimg.cn/large/0081Kckwgy1gk663vdjh7j30xe0iwqdq.jpg)

  ![截屏2020-10-29 下午2.04.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gk664i1jhij3194094wg9.jpg)

  如果找不到exception handler，那么这个系统就挂了

- The code that might throw certain exceptions must be enclosed by either of the following:

  ![截屏2020-10-29 下午2.15.53](https://tva1.sinaimg.cn/large/0081Kckwgy1gk66gaxx3qj315o09m767.jpg)

- java系统中的三种exceptions

  ![截屏2020-10-29 下午2.07.46](https://tva1.sinaimg.cn/large/0081Kckwgy1gk667vzfggj311y0hytlo.jpg)

- Checked Exception(编译器会检查)

  ![截屏2020-10-29 下午2.08.49](https://tva1.sinaimg.cn/large/0081Kckwgy1gk668yy0naj31760fm77w.jpg)

  Checked exceptions are subject to the Catch or Specify Requirement. All exceptions are checked exceptions, expect for thos e indicated by Error, RuntimeException, and their subclasses

- Error

  ![截屏2020-10-29 下午2.10.40](https://tva1.sinaimg.cn/large/0081Kckwgy1gk66aw0tsoj31940kcgq1.jpg)

- Runtime Exception(编译时不会检查)

  ![截屏2020-10-29 下午2.12.37](https://tva1.sinaimg.cn/large/0081Kckwgy1gk66cxt0zbj319w0giwif.jpg)

  一般需要求程序员来解决

- error和runtime exceptions被统称为不受检查的异常(unchecked exceptions)

- Try with Resources(在该block里面定义的资源会自动释放)

  ![截屏2020-10-29 下午2.37.12](https://tva1.sinaimg.cn/large/0081Kckwgy1gk672hqox4j319e0jyae8.jpg)

  ![截屏2020-10-29 下午2.40.45](https://tva1.sinaimg.cn/large/0081Kckwgy1gk67670sh0j317q0u0aem.jpg)

  ![截屏2020-10-29 下午2.40.28](https://tva1.sinaimg.cn/large/0081Kckwgy1gk675w7dsbj31aa0ekacc.jpg)

  实现了`java.lang.AutoCloseable`的接口可以在try-with-resources的block里使用

- specifying the Exceptions Thrown by a Method

  按照如下形式

  ```java
  public void writeList() thorws IOException, IndexOutOfBoundsException {}
  ```

- The throw Statement

  - The throw statement requires a single argument: a throwable object
  - Throwable objects are instances of any subclass of the Throwable class
  - You can throw only objects that inherit from the java.lang.Throwable class

- Throwable Class and Its Subclasses

  - Error Class

    ![截屏2020-10-29 下午3.00.11](https://tva1.sinaimg.cn/large/0081Kckwgy1gk67qg6hpdj312e05u75c.jpg)

    一般的程序不会catch或者throw Errors

  - Exception Class

    An Exception indicates that a problem occurred, but it is not a serious system problem

- Creating Exception classes

  ```java
  class MyException extends Exception {
    private int id;
    public Myexception(String message, int id) {
      super(message);
      this.id = id;
    }
    
    public int getId() {
      return id;
    }
  }
  ```

- Unchecked Exceptions - The Controversy

  两个原则：

  - If a client can reasonably be expected to recover from an exception, make it a checked exception
  - If a client cannot do anything to recover from the exception, make it an unchecked exception

  

