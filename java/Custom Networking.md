### Custom Networking

---

- port

  Ports are identified by a 16-bit number, which TCP and UDP use to deliver the data to the right application.

- **常见的端口号**

- **Networking Classes in the JDK**

  - The URL, URLConnection, Socket, and ServerSocket classes all use TCP to communicate over the network.
  - The DatagramPacket, DatagramSocket, and MulticastSocket classes are for use with UDP.

- url

  **url可以带端口**

  - Java programs can use a class called URL in the java.net package to represent a URL address.

  - A URL has two main components:

    - –  Protocol identifier: For the URL http://example.com,

      the protocol identifier is http.

    - –  Resource name: For the URL http://example.com, the resource name is example.com.

- 创建url

  `URL myURL = new URL("http://example.com/");`

  ![截屏2020-11-12 下午2.30.05](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdjgq66rj314k0qidle.jpg)

  ![截屏2020-11-12 下午2.31.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdkdoswrj31aa0notce.jpg)

- 解析url

  ![截屏2020-11-12 下午2.33.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdmw6gxfj31840r2dly.jpg)

- **Reading Directly from a URL**

  ![截屏2020-11-12 下午2.35.00](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdojr2ioj317y0gc0v7.jpg)

  ![截屏2020-11-12 下午2.35.07](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdoohqlfj313w0qaae1.jpg)

- connecting to a url

  ```java
  URL myURL = new URL("http://example.com/");
  URLConnection myURLConnection = myURL.openConnection();
  ```

  ![截屏2020-11-12 下午2.39.01](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdspfrdcj31840ny789.jpg)

  不一定显示调用connect来初始化连接

- reading from a url connection

  ![截屏2020-11-12 下午2.39.53](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmdtlabxqj315k0qeq72.jpg)

- write to a urlConnection

  - If the URL does not support output, getOutputStream method throws an UnknownServiceException.
  - If the URL does support output, then this method returns an output stream that is connected to the input stream of the URL on the server side — the client's output is the server's input.

  ![截屏2020-11-12 下午3.08.54](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmenshid5j31480tgq9s.jpg)

  服务端进行读取

  ![截屏2020-11-12 下午3.10.24](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmepd1vsaj30x00aaac0.jpg)

- SOCKET

  - URLs and URLConnections provide a relatively high-level mechanism for accessing resources on the Internet. Sometimes your programs require lower-level network communication, for example, when you want to write a client-server application.

- Reading from and Writing to a Socket

  ![截屏2020-11-12 下午3.18.11](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmexfz8itj316809i401.jpg)

- Echo Protocol

  简单来讲就是就收什么数据就发送什么数据

  ![截屏2020-11-12 下午3.24.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmf3v9hh8j317a0o8dkq.jpg)

- EchoServer

  ![截屏2020-11-12 下午3.23.51](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmf3cq5czj31ag0tidmx.jpg)

- Datagrams

  - The DatagramPacket and DatagramSocket classes in the java.net package implement system-independent datagram communication using UDP.

  ![截屏2020-11-12 下午3.28.29](https://tva1.sinaimg.cn/large/0081Kckwgy1gkmf897zt5j315g0hi41w.jpg)

  