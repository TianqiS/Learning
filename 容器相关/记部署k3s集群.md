### 记部署k3s集群

---

注意：

- image的名字一定要正确
- 可以使用`kubectl describe pods`来查看pod没有正确部署的原因

---

#### 使用别人的image文件进行部署

- 部署service

  新建`se.yaml`文件

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    labels:
      app: kubernetes-bootcamp-v1
    name: kubernetes-bootcamp-v1
  spec:
    ports:
    - port: 8080
      protocol: TCP
      targetPort: 8080
    selector:
      app: kubernetes-bootcamp-v1
    type: ClusterIP
  ```

  之后调用`kubectl apply -f -se.yaml`

  ![image-20201207153307156](https://tva1.sinaimg.cn/large/0081Kckwgy1glfbun1l6ej30li01kaas.jpg)

  之后`kubectl get service`

  ![image-20201207153335097](https://tva1.sinaimg.cn/large/0081Kckwgy1glfbv4f3onj30x6034di6.jpg)

  尝试访问service

  `curl 10.43.114.144:8080`

  ![image-20201207153359613](https://tva1.sinaimg.cn/large/0081Kckwgy1glfbvjpsxnj30y201imyf.jpg)

- 我们不需要再单独安装其他ingress controller，因为k3s已经内置了Traefik，直接创建Ingress:

  ```yaml
  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: "int32bit-test-ingress"
    labels:
      app: int32bit-test-ingress
  spec:
    rules:
      - host: test.int32bit.me
        http:
          paths:
          - path: /v1
            backend:
              serviceName: "kubernetes-bootcamp-v1"
              servicePort: 8080
  ```

  `kubectl apply -f ingress.yaml`

  `kubectl get ingress`

  ![image-20201207154111996](https://tva1.sinaimg.cn/large/0081Kckwgy1glfc3275xdj3116038mzt.jpg)

  其中`172.21.0.16`为服务器的地址

  使用ifconfig打印查看

  ![image-20201207154157817](https://tva1.sinaimg.cn/large/0081Kckwgy1glfc3uphjdj30sy07078l.jpg)

  由于没有`test.int32bit.me`，因此需要修改本地DNS，修改/etc/hosts文件

  ![image-20201207154326855](https://tva1.sinaimg.cn/large/0081Kckwgy1glfc5dw1ezj30cc00umxa.jpg)

  之后可以通过域名访问![image-20201207154431075](https://tva1.sinaimg.cn/large/0081Kckwgy1glfc6i2u6lj30yk01m0tz.jpg)

  <span style="color:red">问题：这里导出的ip地址很明显是一个内网的地址，该如何让外部服务器访问到这个地址呢？ nginx反向代理吗？</span>

  不需要这样搞，创建一个type类型为nodePort的service即可

  

---

#### 自己创建Image并部署上去

这里采用的是阮一峰的koa-demo作为部署的服务

1. 将koa-demo clone到本地来

   ```bash
   git clone https://github.com/ruanyf/koa-demos.git
   ```

2. 编写`.dockerignore`

   ![image-20201208113330112](https://tva1.sinaimg.cn/large/0081Kckwgy1glgajoa5hmj309q034weu.jpg)

3. 编写Dockerfile文件

   ![image-20201208113359595](https://tva1.sinaimg.cn/large/0081Kckwgy1glgak5h9edj30nm052dhm.jpg)

   其中`CMD node demos/02.js`表示当容器创建成功之后自动运行`node demos/02.js`命令

4. 创建docker image `docker build -t koa-demo .`

5. 创建成功之后通过`docker image ls`来查看创建好的image

   ![image-20201208113829227](https://tva1.sinaimg.cn/large/0081Kckwgy1glgaotq8agj313e09otgf.jpg)

6. 编写yaml文件来部署deployment，内容如下

   ![image-20201208113530948](https://tva1.sinaimg.cn/large/0081Kckwgy1glgalq7nt8j30f20g60wn.jpg)

   要注意`containers`下面那个`-`，不加的话会报错，同时要注意image的名字一定要写对，这里我踩了好多的坑，最后查了好多报错原因发现自己image名字写错了

7. 创建好yaml文件之后就可以启动deployment来部署pod了

   启动命令: `kubectl apply -f ./de.yaml`

   创建完成之后可以通过`kubectl get deployment`来查看创建好的deployment(如果没有创建成功的话可以通过`kubectl describe pods`来查看pod没有成功创建的原因)

   ![image-20201208114037420](https://tva1.sinaimg.cn/large/0081Kckwgy1glgar1obsvj30q202ggmp.jpg)

   可以发现已经创建好了

8. 接下来就是部署service将服务暴露出来了，编辑se.yaml

   ![image-20201208135128916](https://tva1.sinaimg.cn/large/0081Kckwgy1glgej71i1jj30i40e8dj4.jpg)

   port是service需要暴露的端口列表

   targetPort是需要转发到后端Pod的端口号

9. 使用`kubectl apply -f se.yaml`部署service

10. 通过`kubectl get service`查看部署的service

    ![image-20201208135318688](https://tva1.sinaimg.cn/large/0081Kckwgy1glgel3ulccj30tm03gdi1.jpg)

    发现已经部署好了

11. 调用`curl 10.43.217.127:8080`访问服务

    ![image-20201208135410205](https://tva1.sinaimg.cn/large/0081Kckwgy1glgelzh122j30ne01qaay.jpg)

<span style="color:red">这里有一个问题，因为我们的service类型为ClusterIp的，只能通过集群内部的pod才能访问到该服务，那么为什么在集群外部也可以访问到该服务呢？</span>

答：这里我错误的认为node节点本身是处于集群外部的，但其实node节点也算集群内部，并不算是集群外部

使用`kubectl describe pods`查看每个pods的ip信息

![image-20201208152704103](https://tva1.sinaimg.cn/large/0081Kckwgy1glghanu2bij30nq0ak0u2.jpg)

因为当我们使用`ping 10.42.0.29`的时候，是可以访问到pod的，同时使用`curl 10.42.0.29：3000`端口是可以正常访问的

---

#### 使用Ingress来暴露服务

1. 由于k3s内部已经内置了Traefik，因此不需要再单独安装其他ingress controller，直接创建Ingress的yaml文件

   ![image-20201208140117231](https://tva1.sinaimg.cn/large/0081Kckwgy1glgeteq9vrj30ju0ca0w2.jpg)

2. 创建ingress，`kubectl apply -f ingress.yaml`

   ![image-20201208140217711](https://tva1.sinaimg.cn/large/0081Kckwgy1glgeug8cjmj319k02gtau.jpg)

3. 调用`kubectl get ingress`查看创建好的ingress

   ![image-20201208140404938](https://tva1.sinaimg.cn/large/0081Kckwgy1glgewavhd2j31ay034mzp.jpg)

4. 需要修改`/etc/hosts`文件，修改本地DNS

   ![image-20201208141020755](https://tva1.sinaimg.cn/large/0081Kckwgy1glgf2tx81xj309c012glo.jpg)

5. ![image-20201208141036577](https://tva1.sinaimg.cn/large/0081Kckwgy1glgf33pyg4j30lu01qjs8.jpg)

   可以通过域名访问服务了

---

#### 搭建NodePort类型的service

1. nodePort.yaml

   ![image-20201208162402982](https://tva1.sinaimg.cn/large/0081Kckwgy1glgixzgh5aj30eq0bidio.jpg)

   <span style="color:red">这里有个疑问就是ports里面的port可以和之前的创建好的service中的port重复吗？</span>

   可以重复的，个人理解为这个port只是针对当前service来讲的，如下图所示我们可以通过`10.43.33.29`的8080端口访问到服务，也可以通过`10.43.217.127`的8080端口来访问到服务

   ![image-20201208162747923](https://tva1.sinaimg.cn/large/0081Kckwgy1glgj1unnp5j30yy066439.jpg)

2. `kubectl apply -y nodePort.yaml`

3. `kubectl get service `发现服务已经创建好

   ![image-20201208162912663](https://tva1.sinaimg.cn/large/0081Kckwgy1glgj3bdvhcj30zq03wq5u.jpg)

4. 新开启一个终端，访问部署k3s服务的服务器(`152.136.98.65`)上的30309端口

   ![image-20201208163050616](https://tva1.sinaimg.cn/large/0081Kckwgy1glgj50g79qj30p001i0tb.jpg)

   可以发现能够正常访问

   

---

#### 踩的坑

![image-20201207150103676](https://tva1.sinaimg.cn/large/0081Kckwgy1glfaxa6yszj313w07stfg.jpg)

`kubectl describe pods metrics-server-7b4f8b595-fl8f4 -n kube-system`之后发现

![image-20201207150133013](https://tva1.sinaimg.cn/large/0081Kckwgy1glfaxtd6sbj30vo03eabf.jpg)

一直在拉取镜像，不ok

`kubectl describe pods svclb-traefik-xjx2c -n kube-system`

![image-20201207150535491](https://tva1.sinaimg.cn/large/0081Kckwgy1glfb207odyj30ug03adh4.jpg)

需要修改运行时的源

##### 将运行时换成docker

将 K3S 的默认容器引擎从 Containerd 切换到 Docker。

修改 K3S 服务的配置文件：

```bash
vim /etc/systemd/system/multi-user.target.wants/k3s.service
```

文件内容如下：

```bash
[Unit]Description=Lightweight KubernetesDocumentation=https://k3s.ioAfter=network-online.target
[Service]Type=notifyEnvironmentFile=/etc/systemd/system/k3s.service.envExecStartPre=-/sbin/modprobe br_netfilterExecStartPre=-/sbin/modprobe overlayExecStart=/usr/local/bin/k3s serverKillMode=processDelegate=yesLimitNOFILE=infinityLimitNPROC=infinityLimitCORE=infinityTasksMax=infinityTimeoutStartSec=0Restart=always
[Install]WantedBy=multi-user.targe
```

在这里我们需要修改 ExecStart 的值，将其修改为：

```bash
/usr/local/bin/k3s server --docker --no-deploy traefik
```

之后保存退出，执行命令重新加载新的服务配置文件：

```bash
systemctl daemon-reload
```

在之后需要将docker的源设置为国内源

##### 将docker换成国内源

**修改docker镜像仓库配置**

修改/etc/docker/daemon.json文件，如果没有先建一个即可

```shell
sudo vim /etc/docker/daemon.json
```

**修改配置文件**

```shell
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```

**使配置文件生效**

```shell
sudo systemctl daemon-reload
```

**重启Docker**

```shell
sudo service docker restart
```

**测试配置是否成功**

```shell
docker search nginx
```



进入k3d创建的容器的方法

`docker exec -it 容器名 /bin/sh`

否则会报出`OCI runtime exec failed: exec failed`错误





---

### 参考资料

http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html

https://mp.weixin.qq.com/s/gtw6k-jmtatlk8LSkMzGiA