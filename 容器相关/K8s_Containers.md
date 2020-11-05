### K8s Containers

---

A [container image](https://kubernetes.io/docs/concepts/containers/images/) is a ready-to-run software package, containing everything needed to run an application: the code and any runtime it requires, application and system libraries, and default values for any essential settings.

By design, a container is immutable(不变的): you cannot change the code of a container that is already running. If you have a containerized application and want to make changes, you need to build a new image that includes the change, then recreate the container to start from the updated image.

---

#### Images

A container image represents binary data that encapsulates an application and all its software dependencies. Container images are executable software bundles that can run standalone and that make very well defined assumptions about their runtime environment.

You typically create a container image of your application and push it to a registry before referring to it in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/)

Image与Container之间的联系？
答：镜像的概念更多偏向于一个环境包，这个环境包可以移动到任意的Docker平台中去运行；而容器就是你运行环境包的实例。你可以针对这个环境包运行N个实例。换句话说container是images的一种具体表现形式。你也可以认为镜像与你装载操作系统iso镜像是一个概念，容器则可理解为镜像启动的操作系统。一个镜像可以启动任意多个容器，即可以装载多个操作系统。

镜像与容器的先后顺序是什么？

答：当然是先有镜像再有实例了，虽然创建镜像可以参考某个容器，但是标准的做法是先制作镜像然后再跑容器。