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

 #### Self-registration of Nodes

When the kubelet flag `--register-node` is true (the default), the kubelet will attempt to register itself with the API server. This is the preferred pattern, used by most distros.

For self-registration, the kubelet is started with the following options:

- `--kubeconfig` - Path to credentials to authenticate itself to the API server.

- `--cloud-provider` - How to talk to a [cloud provider](https://kubernetes.io/docs/reference/glossary/?all=true#term-cloud-provider) to read metadata about itself.

- `--register-node` - Automatically register with the API server.

- `--register-with-taints` - Register the node with the given list of [taints](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) (comma separated `<key>=<value>:<effect>`).

  No-op if `register-node` is false.

- `--node-ip` - IP address of the node.

- `--node-labels` - [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels) to add when registering the node in the cluster (see label restrictions enforced by the [NodeRestriction admission plugin](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#noderestriction)).

- `--node-status-update-frequency` - Specifies how often kubelet posts node status to master.

#### Node status

A Node's status contains the following information:

- [Addresses](https://kubernetes.io/docs/concepts/architecture/nodes/#addresses)
- [Conditions](https://kubernetes.io/docs/concepts/architecture/nodes/#condition)
- [Capacity and Allocatable](https://kubernetes.io/docs/concepts/architecture/nodes/#capacity)
- [Info](https://kubernetes.io/docs/concepts/architecture/nodes/#info)

You can use `kubectl` to view a Node's status and other details:

`kubectl describe node <insert-node-name-here>`

**Addresses**

The usage of these fields varies depending on your cloud provider or bare metal configuration.

- HostName: The hostname as reported by the node's kernel. Can be overridden via the kubelet `--hostname-override` parameter.
- ExternalIP: Typically the IP address of the node that is externally routable (available from outside the cluster).
- InternalIP: Typically the IP address of the node that is routable only within the cluster.

**conditions**

The `conditions` field describes the status of all `Running` nodes. Examples of conditions include:

| Node Condition       | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `Ready`              | `True` if the node is healthy and ready to accept pods, `False` if the node is not healthy and is not accepting pods, and `Unknown` if the node controller has not heard from the node in the last `node-monitor-grace-period` (default is 40 seconds) |
| `DiskPressure`       | `True` if pressure exists on the disk size--that is, if the disk capacity is low; otherwise `False` |
| `MemoryPressure`     | `True` if pressure exists on the node memory--that is, if the node memory is low; otherwise `False` |
| `PIDPressure`        | `True` if pressure exists on the processes—that is, if there are too many processes on the node; otherwise `False` |
| `NetworkUnavailable` | `True` if the network for the node is not correctly configured, otherwise `False` |

The node condition is represented as a JSON object. For example, the following structure describes a healthy node:

```json
"conditions": [
  {
    "type": "Ready",
    "status": "True",
    "reason": "KubeletReady",
    "message": "kubelet is posting ready status",
    "lastHeartbeatTime": "2019-06-05T18:38:35Z",
    "lastTransitionTime": "2019-06-05T11:41:27Z"
  }
]
```

If the Status of the Ready condition remains `Unknown` or `False` for longer than the `pod-eviction-timeout` (an argument passed to the [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)), all the Pods on the node are scheduled for deletion by the node controller. The default eviction timeout duration is **five minutes**. In some cases when the node is unreachable, the API server is unable to communicate with the kubelet on the node. The decision to delete the pods cannot be communicated to the kubelet until communication with the API server is re-established. In the meantime, the pods that are scheduled for deletion may continue to run on the partitioned node.(Unknown或者false时间过长的话，pods会被定时删掉)

The node controller does not force delete pods until it is confirmed that they have stopped running in the cluster. You can see the pods that might be running on an unreachable node as being in the `Terminating` or `Unknown` state. In cases where Kubernetes cannot deduce from the underlying infrastructure if a node has permanently left a cluster, the cluster administrator may need to delete the node object by hand. Deleting the node object from Kubernetes causes all the Pod objects running on the node to be deleted from the API server, and frees up their names.

The node lifecycle controller automatically creates [taints](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) that represent conditions. The scheduler takes the Node's taints into consideration when assigning a Pod to a Node. Pods can also have tolerations which let them tolerate a Node's taints.