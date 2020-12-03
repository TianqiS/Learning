### Service

---

pod之间可以直接相互访问吗？

答案是不可以，因为 pod 是有生命周期的，他可能随时被创建也可能随时被销毁，而 **每次新建 pod 就会给其分配一个随机的 ip**，并且 k8s 也会自动调控 pod 的数量。这就导致了 pod 之间直接访问是不现实的，如果有一个入口，**可以动态绑定那些提供相同服务的 pod，并将其开放在固定端口上**，这样访问起来不就方便很多了么？这个入口在 k8s 中被称为`service`。

![截屏2020-12-02 下午4.38.06](https://tva1.sinaimg.cn/large/0081Kckwgy1gl9lmtjttuj30me0b1tbw.jpg)





---

附一个参数说明

![这里写图片描述](https://img-blog.csdn.net/20171208160333046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQTYzMjE4OTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考资料：

https://www.jianshu.com/p/9fae09876eb7