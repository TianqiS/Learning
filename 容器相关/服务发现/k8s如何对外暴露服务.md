### k8s如何对外暴露服务

---

#### port-forward方法

首先来看一下最简单粗暴的方法，我们可以通过一条 k8s 命令来将指定的 pod 端口映射到本地的端口上，基本用法如下：



```xml
kubectl port-forward <资源类型>/<资源名> <本机端口>:<资源端口>
```

例如，我有个 pod 叫做`kubia`，它通过`80`端口对外提供服务，那么我就可以使用`kubectl port-forward pod/kubia 8080:80`将其映射到本机的`8080`端口上。你可以使用`kubectl port-forward -h`查看更多用法。

启用后会占用当前终端的标准输出，可以在后面添加`&`来指定后台运行：



```ruby
root@master1:~# k port-forward kubia-5k2zr 8080:8080 &
[1] 13949
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080

root@master1:~# curl http://localhost:8080
Handling connection for 8080
You've hit kubia-5k2zr
```

`port-forward`可以将 pod 临时映射出来，一般用于测试资源是否可用，在生产环境并不会大规模应用。

#### NodePort映射服务到节点端口

相对于上一种`port-forward`来说，这一种要正式的多，`NodePort`可以将其 **转发到所有 k8s 节点的指定端口上**，并且不会像`port-forward`一样在前台运行。我们先来新建一个`NodePort`，新建`nodeport.yaml`文件并填写如下内容，然后使用`kubectl create -f nodeport.yaml`新建。：

![截屏2020-12-02 下午5.19.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gl9mtxa5r3j30lt0m7wke.jpg)

```bash
apiVersion: v1
kind: Service
metadata:
  name: kubia-nodeport
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30123
  selector:
    app: kubia
```

通过`kind`属性可以看到`NodePort`本质上是一个`svc`资源，他通过指定`spec.ports.nodePort`来讲服务映射到节点的端口上，接下来我们就可以访问下试试，直接`curl`访问`节点ip + 端口号`即可，你可以通过`kubectl get nodes -o wide`找到所有 k8s 节点的 ip：



```shell
root@master1:~# curl http://192.168.56.11:30123
You've hit kubia-m68bq

root@master1:~# curl http://192.168.56.11:30123
You've hit kubia-flg8w

root@master1:~# curl http://192.168.56.11:30123
You've hit kubia-flg8w

root@master1:~# curl http://192.168.56.11:30123
You've hit kubia-8r2cg

root@master1:~# curl http://192.168.56.11:30123
You've hit kubia-m68bq
```

可以看到，使用了`nodePort`之后，服务被正常的请求，并且也正常的被均衡到每一个 pod 上。

但是这里有个问题，假如我的 k8s 里运行了多个 web 应用服务器，我总不能让用户通过端口号`http://domain:8081`、`http://domain:8082`来访问不同的 web 服务吧。能不能处理成`http://domain/web1`、`http://domain/web2` ...这种形式呢。

#### 通过Ingress暴露服务

ingress 就是一个`nginx`服务器。从本质上说，我们可以直接通过配置`nginx`服务器来实现刚才说的访问方式，但是这样每次`svc`发生变更了我们就要重新手动配置一遍，好麻烦的，于是就有聪明的人想出来了，为什么我们不把复杂的配置操作抽象成一个文件，这样有新的变更的话我们直接修改文件，不就可以避免直接操作`nginx`服务器了么？于是，`ingress`诞生了。记住，**`ingress`本质上就是一个`nginx`和许多个配置文件**。

ingress 有两部分构成，负责转发服务的`nginx-ingress-controller`和每个服务的配置文件`ingress`资源，如下：

![img](https://upload-images.jianshu.io/upload_images/13523736-e385fd7c4c7f74e4.png?imageMogr2/auto-orient/strip|imageView2/2/w/515/format/webp)

每个想要对外暴露的`svc`都需要有一个对应的`ingress`资源（*配置文件*）才能对外提供服务，`ingress`资源并不负责实际的流量转发，它只是告诉`nginx-ingress-controller`应该把流量转发到哪个`svc`。

---

参考资料：

https://www.jianshu.com/p/58976bf0a8a4

http://dockone.io/article/4884