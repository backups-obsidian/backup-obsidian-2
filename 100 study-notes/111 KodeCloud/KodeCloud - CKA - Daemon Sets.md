---
created: 2022-10-10 22:45
updated: 2022-10-10 22:48
---
---
**Links**: [[111 KodeCloud Index]]

---
## Daemon Sets
- Daemon sets are like replica sets in the sense that it *helps us run multiple instances of a pod* but it runs **one copy of pod on each node of our cluster**.
	- Whenever a new node is added to our cluster a replica of the pod is added to that node and when the node is removed the pod is removed.

> [!note]- Daemon set ensures that one copy of the pod is always present in all nodes in the cluster.
> It does this using the Node Affinity rules and the default scheduler.

- Example of daemon sets: Monitoring solution or log viewer
	- ![[attachments/Pasted image 20221010224414.png]]

- *kube-proxy is a good use case of daemon sets*.
- Manifest file for a daemon set is exactly like the replica set except for kind.

- Get all the daemonsets: `k get daemonsets`
