---
created: 2023-05-19 14:58
updated: 2023-05-19 14:58
---
---
**Links**: [[111 KodeCloud Index]]

---
## Node OS Upgrades
- If a *node goes down* and it is *down for more than 5 minutes* then the **pods are terminated from that node**.
	- k8s considers the pod as dead.
	- Once the pods is considered dead if the *pod is a part of a deployment or replicaset then they are recreated on another node*.
- The time *k8s waits for a pod to come back online* is known as **pod eviction timeout** and it is set on the controller manager using `--pod-eviction-timeout=5m0s`.
	- The *default value is 5 minutes*.
	- If a node comes back *online after 5 minutes*, it *will NOT have any pods on it*.
	- If we know that the upgrade is going to be less than 5 minutes then we can perform a quick upgrade and reboot on the pod. But this is unsafe.
- **Safer way to perform an upgrade**:
	- **Drain the node** so that workloads are moved to other nodes of the cluster: `k drain <node-name>`.
		- In general we will have to ignore the daemon sets so: `kubectl drain <node-name> --ignore-daemonsets`
		- The pods are *actually NOT moved* but *gracefully terminated and then recreated on other nodes*.
	- **Once we drain the node, it is also marked as unschedulable**.
	- Now once the upgrade is done then node is still unschedulable.
	- We need to **uncordon it** so that pods can be scheduled on it: `k uncordon <node-name>`.
		- Once the node is *uncordoned* (made schedulable) it *DOES NOT mean that the pods that were drained will come back to it*. 
		- It just means the **any new pods that will be created can be scheduled on this node**.
	- We can mark a node unschedulable using cordon: `k cordon <node-name>`
		- It just marks the node unschedulable, unlike drain it DOES NOT move the workloads. 
		- It just makes sure no more pods can be scheduled on that node.

> [!important]- If there is a *pod that is NOT a part of a replica set* then the *drain command WON'T work*.
> - We will have to remove it forcibly.
> - `kubectl drain node01 --ignore-daemonsets --force`
> - Once the pod with no replicaset is drained forcibly it will be **lost forever**.

- **Steps summary: `Drain -> Perform Upgrade -> Uncordon`**
