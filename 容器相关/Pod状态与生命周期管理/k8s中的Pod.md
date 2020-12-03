### k8s中的Pod

---

Pod 是 kubernetes 中你可以创建和部署的最小也是最简的单位。Pod 代表着集群中运行的进程。

Pod 中封装着应用的容器（有的情况下是好几个容器），存储、独立的网络 IP，管理容器如何运行的策略选项。Pod 代表着部署的一个单位：kubernetes 中应用的一个实例，可能由一个或者多个容器组合在一起共享资源。

> [Docker](https://www.docker.com/) 是 kubernetes 中最常用的容器运行时，但是 Pod 也支持其他容器运行时。

在 Kubernetes 集群中 Pod 有如下两种使用方式：

- **一个 Pod 中运行一个容器**。“每个 Pod 中一个容器” 的模式是最常见的用法；在这种使用方式中，你可以把 Pod 想象成是单个容器的封装，kuberentes 管理的是 Pod 而不是直接管理容器。
- **在一个 Pod 中同时运行多个容器**。一个 Pod 中也可以同时封装几个需要紧密耦合互相协作的容器，它们之间共享资源。这些在同一个 Pod 中的容器可以互相协作成为一个 service 单位 —— 一个容器共享文件，另一个 “sidecar” 容器来更新这些文件。Pod 将这些容器的存储资源作为一个实体来管理。

Pod中可以共享两种资源：

1. 网络

   每个 Pod 都会被分配一个唯一的 IP 地址。Pod 中的所有容器共享网络空间，包括 IP 地址和端口。Pod 内部的容器可以使用 `localhost` 互相通信。Pod 中的容器与外界通信时，必须分配共享网络资源（例如使用宿主机的端口映射）。

2. 存储

   可以为一个 Pod 指定多个共享的 Volume。Pod 中的所有容器都可以访问共享的 volume。Volume 也可以用来持久化 Pod 中的存储资源，以防容器重启后文件丢失。

#### 使用Pod

你很少会直接在 kubernetes 中创建单个 Pod。**因为 Pod 的生命周期是短暂的，用后即焚的实体**。当 Pod 被创建后（不论是由你直接创建还是被其他 Controller），都会被 Kubernetes 调度到集群的 Node 上。直到 Pod 的进程终止、被删掉、因为缺少资源而被驱逐、或者 Node 故障之前这个 Pod 都会一直保持在那个 Node 上。

> 注意：重启 Pod 中的容器跟重启 Pod 不是一回事。Pod 只提供容器的运行环境并保持容器的运行状态，重启容器不会造成 Pod 重启。

**Pod 不会自愈**。如果 Pod 运行的 Node 故障，或者是调度器本身故障，这个 Pod 就会被删除。同样的，如果 Pod 所在 Node 缺少资源或者 Pod 处于维护状态，Pod 也会被驱逐。Kubernetes 使用更高级的称为 Controller 的抽象层，来管理 Pod 实例。虽然可以直接使用 Pod，但是在 Kubernetes 中通常是**使用 Controller 来管理 Pod 的**。

#### Pod和Controller

Controller 可以创建和管理多个 Pod，提供副本管理、滚动升级和集群级别的自愈能力。例如，如果一个 Node 故障，Controller 就能自动将该节点上的 Pod 调度到其他健康的 Node 上。

包含一个或者多个 Pod 的 Controller 示例：

- [Deployment](https://jimmysong.io/kubernetes-handbook/concepts/deployment.html)
- [StatefulSet](https://jimmysong.io/kubernetes-handbook/concepts/statefulset.html)
- [DaemonSet](https://jimmysong.io/kubernetes-handbook/concepts/daemonset.html)

通常，Controller 会用你提供的 Pod Template 来创建相应的 Pod。

#### Pod Templates

Pod 模版是包含了其他 object 的 Pod 定义，例如 [Replication Controllers](https://jimmysong.io/kubernetes-handbook/concepts/replicaset.html)，[Jobs](https://jimmysong.io/kubernetes-handbook/concepts/job.html) 和 [DaemonSets](https://jimmysong.io/kubernetes-handbook/concepts/daemonset.html)。Controller 根据 Pod 模板来创建实际的 Pod。

#### Pod中的共享

- 共享的环境

  Pod中共享的环境包括Linux的namespace、cgroup和其他可能的隔绝环境，这一点跟Docker容器一致。在Pod的环境中，每个容器中可能还有更小的子隔离环境。

- 共享IP地址和端口号

  Pod中的容器共享IP地址和端口号，它们之间可以通过`localhost`互相发现。它们之间可以通过进程间通信，例如[SystemV](https://en.wikipedia.org/wiki/UNIX_System_V)信号或者POSIX共享内存。不同Pod之间的容器具有不同的IP地址，**不能直接通过IPC通信。**

- 共享访问volume的全线

  Pod中的容器也有访问共享volume的权限，这些volume会被定义成pod的一部分并挂载到应用容器的文件系统中。

根据Docker的结构，Pod中的容器共享namespace和volume，**不支持共享PID的namespace**。

#### Pod的UID

就像每个应用容器，pod被认为是临时（非持久的）实体。在Pod的生命周期中讨论过，pod被创建后，被分配一个唯一的ID（UID），调度到节点上，并一致维持期望的状态直到被终结（根据重启策略）或者被删除。如果node死掉了，分配到了这个node上的pod，在经过一个超时时间后会被重新调度到其他node节点上。一个给定的pod（如UID定义的）不会被“重新调度”到新的节点上，而是被一个同样的pod取代，如果期望的话甚至可以是相同的名字，但是会有一个新的UID。

Volume跟pod有相同的生命周期（当其UID存在的时候）。当Pod因为某种原因被删除或者被新创建的相同的Pod取代，它相关的东西（例如volume）也会被销毁和再创建一个新的volume。

#### Pod的持久性

Pod在设计支持就不是作为持久化实体的。在调度失败、节点故障、缺少资源或者节点维护的状态下都会死掉会被驱逐。

通常，用户不需要手动直接创建Pod，而是应该使用controller（例如[Deployments](https://jimmysong.io/kubernetes-handbook/concepts/deployment.html)），即使是在创建单个Pod的情况下。Controller可以提供集群级别的自愈功能、复制和升级管理。

使用集合API作为主要的面向用户的原语在集群调度系统中相对常见，包括[Borg](https://research.google.com/pubs/pub43438.html)、[Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)、[Aurora](https://aurora.apache.org/documentation/latest/reference/configuration/#job-schema)和[Tupperware](https://www.slideshare.net/Docker/aravindnarayanan-facebook140613153626phpapp02-37588997)。

Pod 原语有利于：

- 调度程序和控制器可插拔性
- 支持pod级操作，无需通过控制器API“代理”它们
- 将pod生命周期与控制器生命周期分离，例如用于自举（bootstrap）
- 控制器和服务的分离——端点控制器只是监视pod
- 将集群级功能与Kubelet级功能的清晰组合——Kubelet实际上是“pod控制器”
- 高可用性应用程序，它们可以在终止之前及在删除之前更换pod，例如在计划驱逐、镜像预拉取或实时pod迁移的情况下[#3949](https://github.com/kubernetes/kubernetes/issues/3949)

[StatefulSet](https://jimmysong.io/kubernetes-handbook/concepts/statefulset.html) 控制器支持有状态的Pod。在1.4版本中被称为PetSet。在kubernetes之前的版本中创建有状态pod的最佳方式是创建一个replica为1的replication controller。

#### Pod的终止

因为Pod作为在集群的节点上运行的进程，所以在不再需要的时候能够优雅的终止掉是十分必要的（比起使用发送KILL信号这种暴力的方式）。用户需要能够发起一个删除 Pod 的请求，并且知道它们何时会被终止，是否被正确的删除。用户想终止程序时发送删除pod的请求，在pod可以被强制删除前会有一个宽限期，会发送一个TERM请求到每个容器的主进程。一旦超时，将向主进程发送KILL信号并从API server中删除。如果kubelet或者container manager在等待进程终止的过程中重启，在重启后仍然会重试完整的宽限期。

1. 用户发送删除pod的命令，默认宽限期是30秒；
2. 在Pod超过该宽限期后API server就会更新Pod的状态为“dead”；
3. 在客户端命令行上显示的Pod状态为“terminating”；
4. 跟第三步同时，当kubelet发现pod被标记为“terminating”状态时，开始停止pod进程：
   1. 如果在pod中定义了preStop hook，在停止pod前会被调用。如果在宽限期过后，preStop hook依然在运行，第二步会再增加2秒的宽限期；
   2. 向Pod中的进程发送TERM信号；
5. 跟第三步同时，该Pod将从该service的端点列表中删除，不再是replication controller的一部分。关闭的慢的pod将继续处理load balancer转发的流量；
6. 过了宽限期后，将向Pod中依然运行的进程发送SIGKILL信号而杀掉进程。
7. Kubelet会在API server中完成Pod的的删除，通过将优雅周期设置为0（立即删除）。Pod在API中消失，并且在客户端也不可见。

删除宽限期默认是30秒。 `kubectl delete`命令支持 `—grace-period=<seconds>` 选项，允许用户设置自己的宽限期。如果设置为0将强制删除pod。在kubectl>=1.5版本的命令中，你必须同时使用 `--force` 和 `--grace-period=0` 来强制删除pod。 在 yaml 文件中可以通过 `{{ .spec.spec.terminationGracePeriodSeconds }}` 来修改此值。

##### 强制删除Pod

Pod的强制删除是通过在集群和etcd中将其定义为删除状态。当执行强制删除命令时，API server不会等待该pod所运行在节点上的kubelet确认，就会立即将该pod从API server中移除，这时就可以创建跟原pod同名的pod了。这时，在节点上的pod会被立即设置为terminating状态，不过在被强制删除之前依然有一小段优雅删除周期。

强制删除对于某些pod具有潜在危险性，请谨慎使用。使用StatefulSet pod的情况下，请参考删除StatefulSet中的pod文章。

#### API Object

Pod是kubernetes REST API中的顶级资源类型。

在kuberentes1.6的V1 core API版本中的Pod的数据结构如下图所示：

![img](https://jimmysong.io/kubernetes-handbook/images/kubernetes-pod-cheatsheet.png)

#### Pod生命周期

pod的`phase`字段有如下几种情况：

- 挂起（Pending）：Pod 已被 Kubernetes 系统接受，但有一个或者多个容器镜像尚未创建。等待时间包括调度 Pod 的时间和通过网络下载镜像的时间，这可能需要花点时间。
- 运行中（Running）：该 Pod 已经绑定到了一个节点上，Pod 中所有的容器都已被创建。至少有一个容器正在运行，或者正处于启动或重启状态。
- 成功（Succeeded）：Pod 中的所有容器都被成功终止，并且不会再重启。
- 失败（Failed）：Pod 中的所有容器都已终止了，并且至少有一个容器是因为失败终止。也就是说，容器以非0状态退出或者被系统终止。
- 未知（Unknown）：因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败。

![Pod的生命周期示意图（图片来自网络）](https://jimmysong.io/kubernetes-handbook/images/kubernetes-pod-life-cycle.jpg)

一般来说，Pod 不会消失，直到人为销毁他们。这可能是一个人或控制器。这个规则的唯一例外是成功或失败的 `phase` 超过一段时间（由 master 确定）的Pod将过期并被自动销毁。

有三种可用的控制器：

- 使用 [Job](https://kubernetes.io/docs/concepts/jobs/run-to-completion-finite-workloads/) 运行预期会终止的 Pod，例如批量计算。Job 仅适用于重启策略为 `OnFailure` 或 `Never` 的 Pod。

- 对预期不会终止的 Pod 使用 [ReplicationController](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)、[ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) 和 [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) ，例如 Web 服务器。 ReplicationController 仅适用于具有 `restartPolicy` 为 Always 的 Pod。
- 提供特定于机器的系统服务，使用 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 为每台机器运行一个 Pod 。

所有这三种类型的控制器都包含一个 PodTemplate。建议创建适当的控制器，让它们来创建 Pod，而不是直接自己创建 Pod。这是因为单独的 Pod 在机器故障的情况下没有办法自动复原，而控制器却可以。

如果节点死亡或与集群的其余部分断开连接，则 Kubernetes 将应用一个策略将丢失节点上的所有 Pod 的 `phase` 设置为 Failed。



