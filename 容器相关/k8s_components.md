### k8s components

---

![components-of-kubernetes](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

When you deploy Kubernetes, you get a cluster.

- **Node** 

  A Kubernetes cluster consists of a set of worker machines, called [nodes](https://kubernetes.io/docs/concepts/architecture/nodes/),hat run containerized applications. Every cluster has at least one worker node.

- **Pods**

  在Kubernetes集群中，Pod是创建、部署和调度的基本单位。一个Pod代表着集群中运行的一个进程，它内部封装了一个或多个应用的容器。在同一个Pod内部，多个容器共享存储、网络IP，以及管理容器如何运行的策略选项。Docker是Kubernetes中最常用的容器运行时。
  在Kubrenetes集群中，Pod有两种使用方式，如下所示：

  1. 一个Pod中运行一个容器

     这种模式是最常见的用法，可以把Pod想象成是单个容器的封装，Kubernetes直接管理的是Pod，而不是Pod内部的容器。

  2. 一个Pod中同时运行多个容器

     一个Pod中也可以同时运行几个容器，这些容器之间需要紧密协作，并共享资源。这些在同一个Pod中的容器可以互相协作，逻辑上代表一个**Service对象**。每个Pod都是应用的一个实例，如果我们想要运行多个实例，就应该运行多个Pod。
     同一个Pod中的容器，会自动的分配到同一个Node上。**每个Pod都会被分配一个唯一的IP地址，该Pod中的所有容器共享网络空间，包括IP地址和端口**。Pod内部的容器可以使用localhost互相通信。Pod中的容器与外界通信时，必须分配共享网络资源（例如，使用宿主机的端口映射）。
     我们可以为一个Pod指定多个共享的Volume，它内部的所有容器都可以访问共享的Volume。Volume也可以用来持久化Pod中的存储资源，以防容器重启后文件丢失。
     我们可以使用Kubernetes中抽象的Controller来创建和管理多个Pod，提供副本管理、滚动升级和集群级别的自愈能力。当Pod被创建后，都会被Kuberentes调度到集群的Node上，直到Pod的进程终止而被移除掉。

  represents a set of running container in your cluster.

- **Control plane**

  manages the worker nodes and the Pods in the cluster.

  The control plane's components make global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (for example, starting up a new [pod](https://kubernetes.io/docs/concepts/workloads/pods/) when a deployment's `replicas` field is unsatisfied). 

  可以在任何集群的任何机器上装，但是for simplicity, set up scripts typically start all control plane components on the same machine, and do not run user containers on this machine. 

- **Kube-apiserver**

  想要操作Kubernetes集群中的任何对象，都需要经过kube-apiserver，它封装了对Kubernetes对象的所有操作，以REST接口方式提供给想要与Kubernetes交互的任何客户端。经过kube-apiserver的所有对Kubernetes对象的修改操作都将持久化到etcd存储中。

  The API server is a component of the Kubernetes [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane) that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.

  The main implementation of a Kubernetes API server is [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/). kube-apiserver is designed to scale horizontally—that is, it scales by deploying more instances. You can run several instances of kube-apiserver and balance traffic between those instances.

- **etcd**

  Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

- Kube-scheduler

  负责Kubernetes集群内部的资源调度，主要负责监视Kubernetes集群内部已创建但没有被调度到某个Node上的Pod，然后将该Pod调度到一个选定的Node上面运行。

  Control plane component that watches for newly created [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) with no assigned [node](https://kubernetes.io/docs/concepts/architecture/nodes/), and selects a node for them to run on.

- Kube-controller-manager

  Control Plane component that runs [controller](https://kubernetes.io/docs/concepts/architecture/controller/) processes.

  Logically, each [controller](https://kubernetes.io/docs/concepts/architecture/controller/) is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.

  These controllers include:

  - Node controller: Responsible for noticing and responding when nodes go down.
  - Replication controller: Responsible for maintaining the correct number of pods for every replication controller object in the system.
  - Endpoints controller: Populates the Endpoints object (that is, joins Services & Pods).
  - Service Account & Token controllers: Create default accounts and API access tokens for new namespaces.

- Cloud-controller-manager

  A Kubernetes [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane) component that embeds cloud-specific control logic. The cloud controller manager lets you link your cluster into your cloud provider's API, and separates out the components that interact with that cloud platform from components that just interact with your cluster.

  The cloud-controller-manager only runs controllers that are specific to your cloud provider. If you are running Kubernetes on your own premises, or in a learning environment inside your own PC, the cluster does not have a cloud controller manager.

  As with the kube-controller-manager, the cloud-controller-manager combines several logically independent control loops into a single binary that you run as a single process. You can scale horizontally (run more than one copy) to improve performance or to help tolerate failures.

  The following controllers can have cloud provider dependencies:

  - Node controller: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding
  - Route controller: For setting up routes in the underlying cloud infrastructure
  - Service controller: For creating, updating and deleting cloud provider load balancers

---

#### Node Components

Node components run on every node, maintaining running pods and providing the Kubernetes runtime environment.

在每一个节点上运行，提供运行环境和保持running pods

- Kubelet

  kubelet负责监视指派到它所在Node上的Pod，还负责处理如下工作：

  - 为Pod挂载Volume
  - 下载Pod拥有的Secret
  - 运行属于Pod的容器
  - 周期性地检查Pod中的容器是否存活
  - 向Master报告当前Node上Pod的状态信息
  - 向Master报告当前Node的状态信息

  An agent that runs on each [node](https://kubernetes.io/docs/concepts/architecture/nodes/) in the cluster. It makes sure that [containers](https://kubernetes.io/docs/concepts/containers/) are running in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/).

  The kubelet takes a set of PodSpecs that are provided through various mechanisms and ensures that the containers described in those PodSpecs are running and healthy. The kubelet doesn't manage containers which were not created by Kubernetes.

- kube-proxy

  在Kubernetes集群中，每个Node上面都有一个该网络代理进程，它主要负责为Pod对象提供代理：定期从etcd存储中获取所有的Service对象，并根据Service信息创建代理。当某个Client要访问一个Pod时，请求会经过该Node上的代理进程进行请求转发。

  kube-proxy is a network proxy that runs on each [node](https://kubernetes.io/docs/concepts/architecture/nodes/) in your cluster, implementing part of the Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) concept.

  [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.
  
  kube-proxy uses the operating system packet filtering layer if there is one and it's available. Otherwise, kube-proxy forwards the traffic itself.

---

#### Container runtime

The container runtime is the software that is responsible for running containers.

Kubernetes supports several container runtimes: [Docker](https://docs.docker.com/engine/), [containerd](https://containerd.io/docs/), [CRI-O](https://cri-o.io/#what-is-cri-o), and any implementation of the [Kubernetes CRI (Container Runtime Interface)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md).



- Addons(插件)

  Addons use Kubernetes resources ([DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset), [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), etc) to implement cluster features. Because these are providing cluster-level features, namespaced resources for addons belong within the `kube-system` namespace.

  Selected addons are described below; for an extended list of available addons, please see [Addons](https://kubernetes.io/docs/concepts/cluster-administration/addons/).

- DNS

  While the other addons are not strictly required, all Kubernetes clusters should have [cluster DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/), as many examples rely on it.

  Cluster DNS is a DNS server, in addition to the other DNS server(s) in your environment, which serves DNS records for Kubernetes services.

  Containers started by Kubernetes automatically include this DNS server in their DNS searches.

  

---

  #### Service

Kubernetes Service从逻辑上定义了一个Pod的集合以及如何访问这些Pod的策略，有时也被称作是微服务，它与Pod、ReplicaSet等Kubernetes对象之间的关系，描述如下图所示：

![截屏2020-12-01 下午1.37.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8asn4ny2j30or0jydhp.jpg)

上图中，Client请求Service，Service通过Label Selector，将请求转发到对应的一组Pod上。同时，ReplicaSet会基于Label Selector来监控Service对应的Pod的数量是否满足用户设置的预期个数，最终保证Pod的数量和replica的值一致。
在Kubernetes集群中，每个Node都会运行一个kube-proxy，它主要负责为Service实现一种虚拟IP的代理，这种代理有两种模式：

1. userspace模式

   这种模式是在Kubernetes v1.0版本加入的，工作在4层（传输层：TCPUDP over IP），称为userspace模式，如下图所示：

   ![截屏2020-12-01 下午1.46.00](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8b1fw119j30qg0iomza.jpg)

2. iptables模式

   这种模式是在Kubernetes v1.1版本新增的，它工作在七层（应用层：HTTP），称为iptables，如下图所示：

   ![截屏2020-12-01 下午1.46.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8b1xz2e3j30ro0iajto.jpg)

Service定义了4种服务类型，以满足使用不同的方式去访问一个Service，这4种类型包括：ClusterIP、NodePort、LoadBalancer、ExternalName。

- ClusterIP是默认使用的类型，它表示在使用Kubernetes集群内部的IP地址，使用这种方式我们只能在该集群中访问Service。
- NodePort类型，会在Node节点上暴露一个静态的IP地址和端口，使得外部通过NodeIP:NodePort的方式就能访问到该Service。
- LoadBalancer类型，通过使用Cloud提供商提供的负载均衡IP地址，将Service暴露出去。
- ExternalName类型，会使用一个外部的域名来将Service暴露出去（Kubernetes v1.7及以上版本的kube-dns支持该种类型）。

#### Volume

默认情况下容器的数据都是非持久化的，在容器销毁以后数据也会丢失，所以Docker提供了Volume机制来实现数据的持久化存储。同样，Kubernetes提供了更强大的Volume机制和丰富的插件，实现了容器数据的持久化，以及在容器之间共享数据。
Kubernetes Volume具有显式的生命周期，它与Pod的生命周期是相同的，所以只要Pod还处于运行状态，则该Pod内部的所有容器都能够访问该Volume，即使该Pod中的某个容器失败，数据也不会丢失。

#### Ingress

通常情况下，Service和Pod仅可在集群内部网络中通过IP地址访问。所有到达Edge router的流量或被丢弃或被转发到其它地方。一个Ingress是一组规则的集合，这些规则允许外部请求（公网访问）直接连接到Kubernetes集群内部的Service，如下图所示：

![截屏2020-12-01 下午2.10.50](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8br8r9khj30k50gfjsq.jpg)

我们可以配置Ingress，为它提供外部（公网）可访问给定Service的规则，如Service URL、负载均衡、SSL、基于名称的虚拟主机等。用户想要基于POST方式请求Ingress资源，需要通过访问Kubernetes API Server来操作Ingress资源。
Ingress Controller是一个daemon进程，它是通过Kubernetes Pod来进行部署的。它负责实现Ingress，通常使用负载均衡器，还可以配置Edge router和其他前端（frontends），以高可用（HA）的方式来处理请求。为了能够使Ingress工作，Kubernetes集群中必须要有个一个Ingress Controller在运行，不同于kube-controller-manager所管理的Controller，我们必须要选择一个适合我们Kubernetes集群的Ingress Controller实现，如果没有合适的就需要开发实现一个。
定义一个Ingress，示例如下所示：

![截屏2020-12-01 下午2.11.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8brxfsorj30di06it97.jpg)

Ingress有如下5种类型：

- 单Service Ingress
- 简单扇出（fanout）
- 基于名称的虚拟主机
- TLS
- 负载均衡

#### Horizontal Pod Autoscaler

应用运行在容器中，它所依赖的资源的使用率通常并不均衡，资源使用量有时可能达到峰值，有时使用的又很少，为了提高Kubernetes集群的整体资源利用率，我们需要Service中的Pod的个数能够根据实际资源使用量来自动调整。这时我们就可以使用HPA（Horizontal Pod Autoscaling），它和Pod、Deployment等都是Kubernetes API资源，能实现Kubernetes集群内Service中Pod的水平伸缩。
HPA的工作原理，如下图所示：

![截屏2020-12-01 下午2.20.30](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8c1c6hw1j30k50fxab1.jpg)

![截屏2020-12-01 下午2.21.32](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8c2dj0saj30ul07kabi.jpg)

#### Deployment

Deployment提供了声明式的方法，对Pod和ReplicaSet进行更新，我们只需要在Deployment对象中设置好预期的状态，然后Deployment就能够控制将实际的状态保持与预期状态一致。使用Deployment的典型场景，有如下几个：

- 创建ReplicaSet，进而通过ReplicaSet启动Pod，Deployment会检查启动状态是否成功
- 滚动升级或回滚应用
- 应用扩容或缩容
- 暂停（比如修改Pod模板）及恢复Deployment的运行
- 根据Deployment的运行状态，可以判断对应的应用是否hang住
- 清除掉不再使用的ReplicaSet

定义一个Deployment，示例如下所示：

![截屏2020-12-01 下午2.17.24](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8by2k9qwj30nu08cmy2.jpg)

#### DeamonSet

DaemonSet能够保证Kubernetes集群中某些Node，或者全部Node上都运行一个Pod副本，当集群中某个Node被移除时，该Node上的Pod副本也会被清理掉。删除一个DaemonSet，也会把对应的Pod都删除掉。
通常，DaemonSet会被用于如下场景（在Kubernetes集群中每个Node上）：

- 运行一个存储Daemon，如glusterd、ceph等
- 运行一个日志收集Daemon，如fluentd、logstash等
- 运行一个监控Daemon，如collectd、gmond等

定义一个DaemonSet，示例如下所示：

![截屏2020-12-01 下午2.17.55](https://tva1.sinaimg.cn/large/0081Kckwgy1gl8bylnm79j30lf0ghjte.jpg)





