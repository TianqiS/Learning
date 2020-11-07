### Concurrency

---

- Processes and Threads

  - Every application has at least one thread, called the main thread. This thread has the ability to create additional threads.
  - Threads share the process's resources, including memory and open files. This makes for efficient, but potentially problematic communication.
  - A process is a self-contained program running within its own address space.(进程是)
  - A thread is a single sequential flow of control within a process.(线程)

- Thread Objects

  - Each thread is associated with an instance of the class Thread.
  - There are two basic strategies for using Thread objects to create a concurrent application.(2种方法)
  - The first one provides a Runnable object. The Runnable interface defines a single method, run, meant to contain the code executed in the thread. The Runnable object is passed to the Thread constructor.
  - 第一种创建线程的方法

  ![截屏2020-11-06 上午8.24.41](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf59dw7k7j30rc0lmgo2.jpg)

  - 第二种创建线程的方法

  ![截屏2020-11-06 上午8.26.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5b6j4mbj30ns0cujst.jpg)

  Subclass Thread. The Thread class **itself implements Runnable**, though its run method does nothing.

  第一种方法更常用

  The first idiom, which employs a Runnable object, is more general, because the Runnable object can subclass a class other than Thread.

- Pausing Execution with Sleep

  Thread.sleep causes the current thread to suspend execution for a specified period. 

  ![截屏2020-11-06 上午8.30.35](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5fiwl3fj30nw0fatb0.jpg)

- ![截屏2020-11-06 上午8.34.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5jjre6mj30vk0pwjv0.jpg)

  ![截屏2020-11-06 上午8.35.16](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5kd9rarj30sm0audh3.jpg)

- ![截屏2020-11-06 上午8.41.29](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5qxqlyvj31160o0dk4.jpg)

- The Interrupt status flag

  ![截屏2020-11-06 上午8.43.12](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5slyz5bj30we0fiq5z.jpg)

- joins

  ![截屏2020-11-06 上午8.44.42](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf5u7y7rlj30va0g6wh7.jpg)

- Synchronized Methods

  ![截屏2020-11-06 上午9.01.52](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6c1eh2lj30kw0icq4a.jpg)

  ![截屏2020-11-06 上午9.04.05](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6edjaygj313g0mejwb.jpg)

- 同步的实现机制内部锁 intrinsic lock

  Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships that are essential to visibility.

  **Every object has an intrinsic loc k associated with it**

  By convention, a thread that needs exclusive and consistent access to an object's fields has to acquire the object's intrinsic lock before accessing them, and then release the intrinsic lock when it's done with them.

  ![截屏2020-11-06 上午9.07.59](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6ifw52vj311q0m2ae1.jpg)

- 静态方法的锁

  ![截屏2020-11-06 上午9.09.04](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6jj1zstj31040b6q4s.jpg)

- Synchronized statements

  ![截屏2020-11-06 上午9.10.17](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6kuzt68j315e0h640t.jpg)

- Synchronized statements使用示例

  ![截屏2020-11-06 上午9.13.41](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6od281ej30z20kutbv.jpg)

- 进程之间的communication

  有三个function对communication提供技术支持

  ![截屏2020-11-06 上午9.17.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gkf6rtyjxuj310k0aygnx.jpg)

  These methods have been implemented as final methods in Object, so they are available in all the classes. All three methods can be called only from within a synchronized context.

  
