### Nodes

---

**Kubernetes runs your workload by placing containers into Pods to run on *Nodes*.** A node may be a virtual or physical machine, depending on the cluster. Each node contains the services necessary to run [Pods](https://kubernetes.io/docs/concepts/workloads/pods/), managed by the [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane).

Typically you have several nodes in a cluster; in a learning or resource-limited environment, you might have just one.

The [components](https://kubernetes.io/docs/concepts/overview/components/#node-components) on a node include the [kubelet](https://kubernetes.io/docs/reference/generated/kubelet), a [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes), and the [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/).

#### Management

There are two main ways to have Nodes added to the [API server](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver): （有两种方式将Node添加到Api server中）

1. The kubelet on a node self-registers to the control plane
2. You, or another human user, manually add a Node object

After you create a Node object, or the kubelet on a node self-registers, the control plane checks whether the new Node object is valid. For example, if you try to create a Node from the following JSON manifest:

```json
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.240.79.157",
    "labels": {
      "name": "my-first-k8s-node"
    }
  }
}
```

Kubernetes creates a Node object internally (the representation). Kubernetes checks that a kubelet has registered to the API server that matches the `metadata.name` field of the Node. If the node is healthy (if all necessary services are running), it is eligible to run a Pod. Otherwise, that node is ignored for any cluster activity until it becomes healthy.

kubernets会检查invalid node是否能运行，在此期间keeps the object for the invalid node

 