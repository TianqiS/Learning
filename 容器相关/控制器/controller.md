### Controller

---

In Kubernetes, controllers are control loops that watch the state of your [cluster](https://kubernetes.io/docs/reference/glossary/?all=true#term-cluster), then make or request changes where needed. Each controller tries to move the current cluster state closer to the desired state.

#### Controller pattern

controller的作用就是保证object当前状态向`spec`中指定的状态靠齐

A controller tracks at least one Kubernetes resource type. These [objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#kubernetes-objects) have a `spec` field that represents the desired state. The controller(s) for that resource are responsible for making the current state come closer to that desired state.

The controller might carry the action out itself; more commonly, in Kubernetes, a controller will send messages to the [API server](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver) that have useful side effects. 

#### control的方式

- Control via API server

  **Built-in controller manage state by interacting with the cluster API server**

  The [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) controller is an example of a Kubernetes built-in controller. Built-in controllers manage state by interacting with the cluster API server.

  Job is a Kubernetes resource that runs a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/), or perhaps several Pods, to carry out a task and then stop.

  When the Job controller sees a new task it makes sure that, somewhere in your cluster, the kubelets on a set of Nodes are running the right number of Pods to get the work done.(job controller保证任务状态是规定好的那样，通过api server与其他节点进行交互)

  After you create a new Job, the desired state is for that Job to be completed. The Job controller makes the current state for that Job be nearer to your desired state: creating Pods that do the work you wanted for that Job, so that the Job is closer to completion.

  Controllers also update the objects that configure them. For example: once the work is done for a Job, the Job controller updates that Job object to mark it `Finished`.

- Direct control

  By contrast with Job, some controllers need to make changes to things outside of your cluster.

  For example, if you use a control loop to make sure there are enough [Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/) in your cluster, then that controller needs something outside the current cluster to set up new Nodes when needed.

  Controllers that interact with external state find their desired state from the API server, then communicate directly with an external system to bring the current state closer in line.

  (There actually is a [controller](https://github.com/kubernetes/autoscaler/) that horizontally scales the nodes in your cluster.)

  The important point here is that the controller makes some change to bring about your desired state, and then reports current state back to your cluster's API server. Other control loops can observe that reported data and take their own actions.

  In the thermostat example, if the room is very cold then a different controller might also turn on a frost protection heater. With Kubernetes clusters, the control plane indirectly works with IP address management tools, storage services, cloud provider APIS, and other services by [extending Kubernetes](https://kubernetes.io/docs/concepts/extend-kubernetes/) to implement that.

#### Ways of running controllers

Kubernetes comes with a set of built-in controllers that run inside the [kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/). These built-in controllers provide important core behaviors.

The Deployment controller and Job controller are examples of controllers that come as part of Kubernetes itself ("built-in" controllers). Kubernetes lets you run a resilient control plane, so that if any of the built-in controllers were to fail, another part of the control plane will take over the work.

You can find controllers that run outside the control plane, to extend Kubernetes. Or, if you want, you can write a new controller yourself. You can run your own controller as a set of Pods, or externally to Kubernetes. What fits best will depend on what that particular controller does.