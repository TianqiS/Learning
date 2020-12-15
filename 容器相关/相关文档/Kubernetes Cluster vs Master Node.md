# Kubernetes Cluster vs Master Node

原文地址：https://www.suse.com/c/kubernetes-cluster-vs-master-node/

**What is a Cluster?**

In software engineering, a cluster resembles a group of nodes that works together to distribute the work load. Additionally clustering helps in fault tolerance, by having a cluster acting as a secondary (backup) to a primary cluster.

![What is Cluster](https://www.suse.com/c/wp-content/uploads/2019/04/What-is-Cluster_-1024x309.jpg)

**What is Master Node in Kubernetes?**

A master node is a node which controls and manages a set of worker nodes (workloads runtime) and resembles a cluster in Kubernetes. A master node has the following components to help manage worker nodes:

- *Kube-APIServer*, which acts as the frontend to the cluster. All external communication to the cluster is via the API-Server.
- *Kube-Controller-Manager*, which runs a set of controllers for the running cluster. The controller-manager implements governance across the cluster.
- *Etcd*, the cluster state database.
- *Kube Scheduler,* which schedules activities to the worker nodes based on events occurring on the etcd. It also holds the nodes resources plan to determine the proper action for the triggered event. For example the scheduler would figure out which worker node will host a newly scheduled POD.

![What is Master Node in K8s](https://www.suse.com/c/wp-content/uploads/2019/04/What-is-Master-Node-in-K8s_-1024x611.jpg)

**Does this mean that there is no clustering anymore in Kubernetes?**

Do we just have master nodes controlling the workloads running on the minion/worker nodes?

The simple answer is **no**. In Kubernetes world, having a master and a worker/minion node creates a cluster, so you can either create a cluster at the initiation of the master node or Kubernetes will by default create a default cluster for the new master node – every cluster must have at least one master and exactly one active master node running the management of the cluster/worker nodes.

**What happens when the master node goes down? Will the workload(s) and worker node(s) go down, too?**

…Does this mean we cannot have Kubernetes running with 99.99 availability?

Also **no**. The solution to this is known as the High Availability Master Node solution.

**What is a High Availability Master Node solution?**

The objective of the solution is to always avoid orphaning worker/minion node(s) and workload(s) i.e. to always have a master taking care of them and managing them.

Solution principles:

- A cluster will have multiple master nodes.
- Master nodes run in different availability zones
- Only one master node is active and operating the whole set of worker node(s).
- With only one node running as the active master node and the other master nodes running as inactive, on of the inactive nodes takes the lead in case the active node goes down.

![High Availability master node soln](https://www.suse.com/c/wp-content/uploads/2019/04/High-Availability-master-node-soln-1024x372.jpg)

**How does the worker node communicate with the master node – or actually, the kube-api-server?**

Will there be any disruption in communication or business running by the worker nodes?

**No**, as the worker node is communicating to the current active running API server using a DSN name or a proxy or a load balancer which hide the physical location of the active API server.

Note: it is recommended to keep N as an odd number for delivering high tolerance.

**Conclusion:**

The Kubernetes master-worker node architecture is already a cluster solution, but for large deployments a cluster can have multiple master nodes to implement high availability solutions.