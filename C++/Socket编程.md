### Socket编程

- 常用的Socket类型：

  1. 流式Socket(SOCK_STREAM)

     流式是一种面向连接的Socket，针对于面向连接的TCP服务应用

  2. 数据格式Socket(SOCK_DGRAM)

     数据报式Socket是一种无连接的Socket，对应于无连接的UDP服务应用

- 一个完整的网间通信进程需要由两个进程组成，并且只能用**同一种高层协议**，也就是说，不可能通信的一端用TCP，而另一端用UDP

- Socket是什么？

  - 独立于具体协议的网络编程接口
  - 在TCP/IP模型中，主要位于传输层和应用层之间

  ![image-20201028080519654](https://tva1.sinaimg.cn/large/0081Kckwgy1gk4q4femrhj30m20dggov.jpg)

- 一个服务程序通常在一个众所周知的地址监听对服务的请求，也就是说服务进程一直处于休眠状态，直到一个客户对这个服务的地址提出了连接请求。在这个时刻，服务程序被“**唤醒**”并且为客户提供服务—对客户的请求作出适当的反应

- Socket(套接字)

  - 套接字是一个通信终结点，它是Socket应用程序用来在网络上发送或接收数据包的对象
  - 套接字具有类型，与正在运行的进程相关联，并且可以有名称
  - 套接字一般只与使用网际协议组的同一“通信域”中的其他套接字交换数据

  ![image-20201028082334383](https://tva1.sinaimg.cn/large/0081Kckwgy1gk4qndig4tj30t606ataa.jpg)

- 流式套接字(SOCK_STREAM)

  - **提供没有记录边界的数据流，即字节流。字节流能确保以正确的顺序无重复地被送达**
  - 提供了一个面向连接、可靠的数据传输服务

  **通信流程**

  ![image-20201028091856338](https://tva1.sinaimg.cn/large/0081Kckwgy1gk4s8zuaqaj30qg0jstbj.jpg)

- 数据报套接字(SOCK_DGRAM)

  -  数据包套接字支持面向记录的数据流，但不能确保能被送达，也无法确保按照发送顺序或不重复
  -  "有序"指数据包发送顺序到达
  
  - "不重复"指一个特定的数据包只能获取一次
  
  **数据报套接字通信流程**
  
  ![截屏2020-10-29 下午5.36.26](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6c93l1qgj30uk0l2goq.jpg)
  
- 在网络上，一个Socket的表示主要借助于地址和端口来描述

- **字节序**存储方式：

  1. 将低序字节存储在起始地址
  2. 将高序字节存储在其实地址

- 网络字节顺序是TCP/IP中规定好的一种数据表示格式，它与具体的CPU类型、操作系统等无关，从而可以保证数据在不同的主机之间传输时能够被正确解释，网络字节顺序采用**big endian(大端存储)**排序方式

- 主机字节顺序和CPU有关，不同的CPU有不同的字节序类型

- IP地址转换函数

  - `inet_addr()`点分十进制数表示的IP地址转换为网络字节序的IP地址
  - `inet_ntoa()`网络字节序的IP地址转换为点分十进制数表示的IP地址

- 字节排序函数

  - `htonl`: 4字节unsigned long主机字节序转换为网络字节序
  - `ntohl`: 4字节网络字节序转换为主机字节序
  - `htons`: 2字节unsigned long主机字节序转换为网络字节序
  - `ntohs`:2字节网络字节序转换为主机字节序

- 创建SOCKET过程

  - 创建套接口socket()

    应用程序在使用socket通信前，必须要拥有一个套接字，使用`socket()`函数来给应用程序创建一个套接字

    ```C++
    SOCKET socket (
    	int af, //地址族，一般是AF_INET，表示使用IP地址组
      int type, //socket类型，SOCK_STREAM或SOCK_DGRAM
      int protocol //协议类型，通常取0
    )
    ```

  - 关闭套接字

    ```c++
    int closesocket(
    	SOCKET s //要关闭的套接字，它是socket()函数调用成功时返回的值
    )
    ```

  - 对socket addr_in结构的填充

    ```C++
    sockaddr_in addServer, addrClient;
    addServer.sin_family = AF_INET;
    addServer.sin_addr.S_un.S_addr = inet_addr(Local_IP);
    addServer.sin_port = htons(8000);
    ```

    `sockaddr_in`结构体的结构：

    ```C++
    struct sockaddr_in {
      short sa_family; //地址族，指定地址格式，设为AF_INET
      u_short sin_port; //端口号
      struct in_addr sin_addr; //IP地址
      char sin_zero[8] //空字节，设为空
    }
    ```

    `sin_addr`域: `struct in_adddr`

    ```c++
    struct in_addr {
      union {
        struct { //S_un_b结构体
          u_char s_b1, s_b2, s_b3, s_b4;
        } S_un_b; 
        
        struct { //S_un_w结构体
          u_short s_w1, s_w2;
        } S_un_w; 
        
        u_long S_addr //IP地址
      } S_un; //union的名称为S_un
    }
    ```

- 指定本机地址

  当socket()创建了一个套接字后，需要将该套接字与该主机上提供服务的某端口联系在一起，bind()函数用于完成这样的绑定

  ```c++
  int bind(
  	SOCKET s,
    const struct sockaddr FAR* name, //指向SOCKADDR结构的地址
    int namelen //地址参数(name)的长度
  )
  ```

  `sockaddr`结构体

  ```c++
  struct sockaddr {
    u_short sa_family;
    char sa_data[14];
  }
  ```

  - 获取IP地址

    - `gethostname`获取本地主机名
    - `gethostbyname`根据主机名获取相关信息

    ```c++
            //获取本机IP地址
    	char PCname[100]={""};
    	char *IPaddress=NULL;	
    	gethostname(PCname,sizeof(PCname));	 //gethostname
    	printf("Local Hostname is %s.\n",PCname);
    	struct hostent FAR * lpHostEnt=gethostbyname(PCname); //gethostbyname
    	if(lpHostEnt==NULL)
    	{
    		//产生错误
    		printf("gethostbyname failed!\n");
    		return;
    	}
    	LPSTR lpAddr=lpHostEnt->h_addr_list[0]; //获取IP
    	if(lpAddr)
    	{
    		struct in_addr inAddr;
    		memmove(&inAddr,lpAddr,4);
    		IPaddress=inet_ntoa(inAddr);//将一个32位数字表示的IP地址转换成点分十进制IP地址字符串
    		if(sizeof(IPaddress)==0)
    			printf("get host IP failed!\n");
    		else
    			printf("Local HostIP is %s.\n",IPaddress);
    	}
    
    ```

    下面是`hostent`结构体的结构

    ```c++
    struct hostent {
      char* h_name;
      char** h_aliases;
      int h_addrtype;
      int h_length;
      char** h_addr_list;
    }
    ```

- 服务器启动监听`listen()`

  在一个服务器端用socket()调用成功创建了一个套接口，并用bind()函数和一个指定的地址关联后，就需要指示该套接字进入监听连接请求状态

  ```c++
  int listen(
  	SOCKET s, //进行监听的socket
    int backlog //客户端可以连接的请求个数
  )
  ```

- 客户端请求连接`connect()`

  当服务器端建立好套接字并与一个本地地址绑定后，就进入监听状态，等待客户发出连接请求。在客户端套接字建立好之后，就调用connect()函数来与服务器建立连接。

  ```c++
  int connect(
  	SOCKET s, //将要连接的socket
    const struct sockaddr FAR* name, //目标socket地址
    int namelen //地址参数(name)的长度
  )
  ```

  在客户端使用connect请求建立连接时，将激活建立连接的三次握手，用来建立一条到服务器TCP的连接。如果调用该函数前没有调用bind()来绑定本地地址，则由系统隐式绑定一个地址到该套接字

  该函数用在UDP的客户端时，一般很少用connect()函数

- 服务器端接受连接`accpet()`

  在服务器端通过listen()进入监听状态，而accept() 表示可以接受自客户端的connect()发出的请求，双方进入连接状态

  ```c++
  SOCKET accept(
  	SOCKET s,
    struct sockaddr FAR* addr,
    int FAR* addrlen
  )
  ```

  - accept用于面向连接的服务器端，在IP协议族中，只用于TCP服务器端
  - accept接受一个socket的连接请求，同时返回一个新的socket，新的socket用来在服务器与客户端之间传递和接收信息

  accept的第一个参数为服务器的socket描述字，是服务器开始调用socket()函数生成的，称为监听socket描述字；而accept函数返回的是已连接的socket描述字。一个服务器通常通常仅仅只创建一个监听socket描述字，它在该服务器的生命周期内一直存在。内核为每个由服务器进程接受的客户连接创建了一个已连接socket描述字，当服务器完成了对某个客户的服务，相应的已连接socket描述字就被关闭

- 发送数据`send()`

  在已经建立连接的套接字上发送数据

  ```c++
  int send(
  	SOCKET s,
    const char FAR* buf, //发送数据缓冲区
    int len, //缓冲区长度
    int flags //用于控制数据传输方式， 0代表按正常方式发送数据
  )
  //返回值：发送的字节数
  ```

- 接受数据`recv()`

  从套接字上接收数据

  ```c++
  int recv(
  	SOCKET s,
    char FAR* buf, //发送数据缓冲区
    int len, //缓冲区长度
    int flags //用于控制数据传输方式， 0代表按正常方式发送数据
  )
  //返回值：接收到的字节数
  ```

- 关闭套接口 `closesocket()`

  - shutdown函数只关闭读写通道，并不关闭套接字，且套接字所占有的资源将被一直保留到closesocket()调用之前
  - 一个套接口不再使用时一定要关闭这个套接口，以释放与该套接口关联的所有资源，包括等候处理的数据

  ```c++
  int closesocket(
  	SOCKET s
  )
  ```

- 面相连接的C/S程序工作流程(TCP)

  - 服务端工作流程
    - 使用`socket()`函数创建服务器端通信套接字
    - 使用`bind()`函数将创建的套接字与服务器地址绑定
    - 使用`listen()`函数使服务器套接字做好接收连接请求准备
    - 使用`accept()`接收来自客户端由`connect()`函数发出的连接请求
    - 根据连接请求建立连接后，使用`send()`函数发送数据，或者使用`recv()`函数接收数据
    - 使用`closesocket()`函数关闭套接字（可以先用`shutdown()`函数先关闭读写通道）
  - 客户端程序工作流程
    - 使用socket()函数创建客户端套接字
    - 使用connect()函数发出也服务器建立连接的请求（调用前可以不用bind()端口号，由系统自动完成）
    - 连接建立后使用send()函数发送数据，或使用recv()函数接收数据
    - 使用closesocet()函数关闭套接字

  工作流程图如下图所示：

  ![image-20201028203415806](https://tva1.sinaimg.cn/large/0081Kckwgy1gk5brno46tj31320r2wl4.jpg)

- socket缓冲区

  每个socket悲怆见后，都会分配两个缓冲区，输入缓冲区和输出缓冲区
  
  `read()/recv()` 并不立即向网络中传输数据，而是先将数据写入缓冲区中，再由TCP协议将数据从缓冲区发送到目标机器。一旦将数据写入到缓冲区，函数就可以成功返回，不管它们有没有到达目标机器，也不管它们何时被发送到网络，这些都是TCP协议负责的事情。
  
  CP协议独立于`read()/recv()` 函数，数据有可能刚被写入缓冲区就发送到网络，也可能在缓冲区中不断积压，多次写入的数据被一次性发送到网络，这取决于当时的网络情况、当前线程是否空闲等诸多因素，不由程序员控制。
  
  下图即为TCP套接字中的IO缓冲区示意图
  
  ![截屏2020-10-29 下午12.55.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gk644u5atfj313s0ccgp5.jpg)
  
    
  
  