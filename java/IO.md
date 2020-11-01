Java I/O

---

Declaration: An I/O Stream represents an input source or an output destination

![截屏2020-10-29 下午3.14.14](https://tva1.sinaimg.cn/large/0081Kckwgy1gk685182llj317q0fa41n.jpg)

下图为从文件中读取信息和向文件中写入信息:

![截屏2020-10-29 下午3.15.04](https://tva1.sinaimg.cn/large/0081Kckwgy1gk685wrn99j30xs0sogyj.jpg)

可供IO读写的数据类型:

![截屏2020-10-29 下午3.17.14](https://tva1.sinaimg.cn/large/0081Kckwgy1gk688532roj31480oqdl4.jpg)

- Java I/O Class Overview Table

  ![截屏2020-10-30 上午8.13.07](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71l92zv6j31ba0sinpd.jpg)
  
- Byte Streams

  程序使用字节流来进行输入和输出

  ![截屏2020-10-30 上午8.15.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71ng9uxuj31ay0k842d.jpg)

- using byte streams

  ![截屏2020-10-30 上午8.17.06](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71pbbsmej30z00tsn1v.jpg)

  下图即为输入输出的运行图

  ![截屏2020-10-30 上午8.18.32](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71qvtgarj31120sih03.jpg)

- always close streams

  ![截屏2020-10-30 上午8.20.26](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71ss8bbmj315a07wmys.jpg)

  ![截屏2020-10-30 上午8.21.28](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71tv8pvuj319g0dqacs.jpg)

- When not to use byte streams

  ![截屏2020-10-30 上午8.24.24](https://tva1.sinaimg.cn/large/0081Kckwgy1gk71wwxhquj318e0dadif.jpg)

  so why talk about byte streams? Because all other stream types are built on byte streams

- Character streams

  ![截屏2020-10-30 上午8.28.53](https://tva1.sinaimg.cn/large/0081Kckwgy1gk721k44f9j31aw0kin18.jpg)

  会自动进行转化字符集

- Using character streams

  - All character stream classes are descended from Reader and Writer
  - 类似字节流，字符流也有一些特殊的IO类，FileReader和FileWriter

  ![截屏2020-10-30 上午8.32.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gk724v0sq2j31500u079f.jpg)

- character streams that use byte streams

  字节流和字符流的关系

  ![截屏2020-10-30 上午8.34.03](https://tva1.sinaimg.cn/large/0081Kckwgy1gk726yf6maj319w0mmjw6.jpg)

- InputStreamReader

  ![截屏2020-10-30 上午8.36.05](https://tva1.sinaimg.cn/large/0081Kckwgy1gk72935k47j31ei0son20.jpg)

- OutputStreamWriter

  ![截屏2020-10-30 上午8.37.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gk72atzjypj31eq0ku78k.jpg)

- Buffered Streams

  为了防止过于频繁的进行访问磁盘，因此在内存中设置一个buffer的区域，

- flushing buffered streams

- PushbackInputStream

  ![截屏2020-10-30 上午8.52.05](https://tva1.sinaimg.cn/large/0081Kckwgy1gk72ppyjsgj317808itab.jpg)

  ![截屏2020-10-30 上午8.52.28](https://tva1.sinaimg.cn/large/0081Kckwgy1gk72q4bvboj3176068myn.jpg)

  PushbackInputStream类似PushbackReader，不同之处是一个处理raw bytes，一个处理characters(text)

- The format Method

  ![截屏2020-10-30 上午9.03.21](https://tva1.sinaimg.cn/large/0081Kckwgy1gk731k406zj310e0ew44n.jpg)

- java File

  Before you can do anything with the file system or File class, you must create a Java File instance.

  ```java
  File file = new File("c:\\data\\input-file.txt");
  ```

  You can check if a file referenced by a Java File object exists using the File exists() method. Here is an example of checking if a file exists:

  ```java
  File file = new File("c:\\data\\input-file.txt");
  boolean fileExists = file.exists();
  ```

  也可以检查目录是否存在

  ```java
  File file = new File("c:\\data"); boolean fileExists = file.exists();
  ```

- **Create a Directory if it Does Not Exist**

  - `mkdir()`

    - The mkdir() method creates a single directory if it does not already exist.

      ```java
      Filefile=newFile("c:\\users\\jakobjenkov\\newdir");
      booleandirCreated=file.mkdir();
      
      ```

      

    - Provided that the directory c:\users\jakobjenkov already exists, the above code will create a subdirectory of jakobjenkov named newdir.

    - The mkdir() returns true if the directory was created, and false if not.

  - `mkdirs()`

    ![截屏2020-10-30 上午9.35.58](https://tva1.sinaimg.cn/large/0081Kckwgy1gk73zdf9z6j31fc0kuadw.jpg)

- **Rename or Move File or Directory**

  - renameTo

    ```java
    Filefile=newFile("c:\\data\\input-file.txt");
    booleansuccess=file.renameTo(newFile("c:\\data\\new-file.txt"));
    ```

  - The renameTo() method can also be used to move a file to a different directory. The new file name passed to the renameTo() method does not have to be in the same directory as the file was already residing in.

    也可以用来移动文件

    返回boolean类型

    对于目录也同样适用

- **Delete File or Directory**

  ```java
  Filefile=newFile("c:\\data\\input-file.txt"); 
  booleansuccess=file.delete();
  ```

  ![截屏2020-10-30 上午9.40.39](https://tva1.sinaimg.cn/large/0081Kckwgy1gk744a0849j31ea0amtb6.jpg)

  