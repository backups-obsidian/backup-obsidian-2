---
created: 2022-10-10 22:45
updated: 2023-05-19 08:23
---
---
**Links**: [[111 KodeCloud Index]]

---
## Daemon Sets
- A Daemonset is **[[Kubernetes - Controllers | another controller]]** that manages pods *like Deployments, ReplicaSets, and StatefulSets*.
- DaemonSets are used in Kubernetes when you need to **run *one* pod on all (or a subset of) the nodes in a cluster**.
	- This means you *CANNOT scale daemonset pods in a node*.
	- Whenever a new node is added to our cluster a replica of the pod is added to that node and when the node is removed the pod is removed.
	- DaemonSets can be *deployed to specific nodes* either by the nodes’ user-defined labels or using values provided by Kubernetes like the node hostname.
	- Diagram:
		- ![[attachments/Pasted image 20230519081733.png]]

> [!note]- Daemon set ensures that *at least one copy of the pod is always present in all nodes in the cluster*.
> It does this using the Node Affinity rules and the default scheduler.

- The typical use case for a DaemonSet is **logging  and monitoring for the hosts**. 
	- For example, a node needs a service (daemon - fluentd) that collects health or log data and pushes them to a central system or database (like *ELK stack*). 
		- ![[attachments/Pasted image 20221010224414.png]]
	- **kube-proxy is another good use case of daemon sets**.
	- Before daemonsets if we wanted to log something we would have *used a cronjob or created a shell script and run it as a service*.

- Manifest file for a daemon set is exactly like the replica set except for kind.
- Get all the daemonsets: `k get daemonsets`
- Daemon sets best practices
	- `DaemonSet` pods **must have Restart Policy** set to `Always` or `unspecified`
	- Separate each `DaemonSet` into its own namespace to ensure clear isolation and easier resource management.
	- `DaemonSet` **must have a priority**. It is not advised for `DaemonSet` Pods to be evicted from cluster nodes for accommodating low priority pods.

### Restrict DaemonSets To Run On Specific Nodes
- By **default**, a *DaemonSet schedules its pods on all the cluster nodes*. 
- But we can restrict daemonset pods on specific nodes using **nodeSelector, nodeAffinity, Taints, and Tolerations**.
	- For example, nodes that host database pods need different monitoring or logging rules. 
	- DaemonSets *allow you to select which nodes* you want to run the pods on. 
		- You can do this by using **nodeSelector**. 
		- *With nodeSelector, you can select nodes by their labels the same way you do with pods*. 
	- However, Kubernetes also allows you to select nodes based on some already-defined node properties. 
		- For example, `kubernetes.io/hostname` matches the node name. 
	- So, our example cluster has two nodes. Modified the DaemonSet definition that runs only on the first node.
		- ![[attachments/Pasted image 20230519080007.png]]

## References
- [Kubernetes Daemonset: A Comprehensive Guide (devopscube.com)](https://devopscube.com/kubernetes-daemonset/)