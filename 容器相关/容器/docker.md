### Docker容器

---

#### 一些常用命令

- **docker container run**

  `docker container run`命令会从 image 文件，生成一个正在运行的容器实例。

  注意，`docker container run`命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。

  在该命令中可以加入一些参数，例如：

  ```bash
  docker container run --rm -p 8000:3000 -it koa-demo /bin/bash
  ```

  其中：

  - `-p`参数：容器的 3000 端口映射到本机的 8000 端口。
  - `-it`参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
  - `koa-demo:0.0.1`：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
  - `/bin/bash`：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。
  - `--rm`: 表示容器结束运行之后会自动删除容器文件

- **docker image pull**

  使用示例：

  `docker image pull library/hello-world`

  上面代码中，`docker image pull`是抓取 image 文件的命令。`library/hello-world`是 image 文件在仓库里面的位置，其中`library`是 image 文件所在的组，`hello-world`是 image 文件的名字。

  由于 Docker 官方提供的 image 文件，都放在[`library`](https://hub.docker.com/r/library/)组里面，所以它的是默认组，可以省略。因此，上面的命令可以写成下面这样。

  `docker image pull hello-world`

- **docker image ls**

  打印本机的image文件

- **docker container ls**

  列出本机正在运行的容器

  下面代码可以列出本机所有的容器，包括终止运行的容器

  `docker container ls -all`

- **docker container rm [containerId]**

  终止运行的容器文件，依然会占据硬盘空间，可以使用[`docker container rm`](https://docs.docker.com/engine/reference/commandline/container_rm/)命令删除。

- **docker container exec**

  `docker container exec`命令用于进入一个正在运行的 docker 容器。如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令进入容器。一旦进入了容器，就可以在容器的 Shell 执行命令了。

  `docker container exec -it [containerID] /bin/bash`

  

---

#### 制作自己的Dockerfile文件

1. 新建`.dockerignore`文件，表示要排除的文件，不打包进入image文件

   ```bash
   .git
   node_modules
   npm-debug.log
   ```

2. 在项目的根目录下新建一个文本文件Dockerfile

   ```bash
   FROM node:8.4
   COPY . /app
   WORKDIR /app
   RUN npm install --registry=https://registry.npm.taobao.org
   EXPOSE 3000
   ```

   上面代码一共五行，含义如下。

   > - `FROM node:8.4`：该 image 文件继承官方的 node image，冒号表示标签，这里标签是`8.4`，即8.4版本的 node。
   > - `COPY . /app`：将当前目录下的所有文件（除了`.dockerignore`排除的路径），都拷贝进入 image 文件的`/app`目录。
   > - `WORKDIR /app`：指定接下来的工作路径为`/app`。
   > - `RUN npm install`：在`/app`目录下，运行`npm install`命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
   > - `EXPOSE 3000`：将容器 3000 端口暴露出来， 允许外部连接这个端口。

3. 创建image文件

   使用`docker image build`命令创建image文件

   ```bash
   $ docker image build -t koa-demo .
   # 或者
   $ docker image build -t koa-demo:0.0.1 .
   ```

   上面代码中，`-t`参数用来指定 image 文件的名字，后面还可以用冒号指定标签。如果不指定，默认的标签就是`latest`。**最后的那个点表示 Dockerfile 文件所在的路径**，上例是当前路径，所以是一个点。

4. 如果运行成功，可以看到新生成的文件了

   使用`docker image ls`打印出新创建的image文件

---

#### 参考资料

[Docker入门教程-阮一峰](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

