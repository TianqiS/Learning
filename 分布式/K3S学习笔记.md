### K3S学习笔记

---

#### K3S简介

K3S是一个轻量级 Kubernetes。安装简单，内存只有一半，所有的二进制都不到 100MB。

K3s 是一个完全符合 Kubernetes 的发行版，有以下增强功能。

- 打包为单个二进制文件。
- 基于 sqlite3 的轻量级存储后端作为默认存储机制。 etcd3，MySQL，Postgres 仍然可用。
- 封装在简单的启动程序中，该启动程序处理很多复杂的 TLS 和选项。
- 默认情况下是安全的，对轻量级环境有合理的默认值。
- 添加了简单但功能强大的“batteries-included”功能，例如：本地存储提供程序，服务负载均衡器，Helm controller 和 Traefik ingress controller。
- 所有 Kubernetes 控制平面组件的操作都封装在单个二进制文件和进程中。这使 K3s 可以自动化和管理复杂的集群操作，例如分发证书。
- 外部依赖性已最小化（仅需要现代内核和 cgroup 挂载）。 K3s 软件包需要依赖项，包括：
  - containerd
  - Flannel
  - CoreDNS
  - CNI
  - 主机实用程序 (iptables, socat, etc)
  - Ingress controller (traefik)
  - 嵌入式 service loadbalancer
  - 嵌入式 network policy controller

#### K3S运行原理

![截屏2020-11-02 下午1.12.08](https://tva1.sinaimg.cn/large/0081Kckwgy1gkar3ez3ylj31ge0u0dky.jpg)



#### k3s和k3d的关系

- k3s: k3s 是由 Rancher Labs 于2019年年初推出的一款轻量级 Kubernetes 发行版，满足在边缘计算环境中运行在 x86、ARM64 和 ARMv7 处理器上的小型、易于管理的 Kubernetes 集群日益增长的需求。
- k3d:  k3d 则是 k3s 社区创建的一个小工具，可以在一个 docker 进程中运行整个 k3s 集群，相比直接使用 k3s 运行在本地，更好管理和部署。

