---
created: 2022-08-28 10:28
updated: 2022-08-28 11:08
---
---
**Links**: [[110 Kubernetes Index]]

---
## Namespaces
- **Namespaces are a way to organise clusters into virtual sub-clusters** by keeping objects separate — they can be *helpful when different teams or projects share a Kubernetes cluster*.
- These namespaces are **logically separated** from each other *but can communicate with each other*. 
	- Namespace groups isolate k8s objects across nodes.
- Test and production objects are in different namespaces in the same cluster.
	- ![[attachments/Pasted image 20220828102802.png]]
- Creating a component in a namespace
	- ![[attachments/Pasted image 20220828102953.png]]

- Kubernetes comes with **four** namespaces out-of-the-box. They are:
	- `default`: As its name implies, this is the namespace that is *referenced by default for every Kubernetes command*, and where **every Kubernetes resource is located by default**. 
	- `kube-system`: Used for Kubernetes components and *should be avoided*.
	- `kube-public`: Used for public resources. *Not recommended for use by users*.
	- `kube-node-lease`: This namespace holds Lease objects associated with each node.
		- Node leases *allow the kubelet to send heartbeats so that the control plane* can detect node failure.

> [!note]- Until new namespaces are created, the entire cluster resides in **default**.
> ![[attachments/Pasted image 20220828103418.png]]

> [!tip]- If you are going to work on a particular namespace a lot then you can change your default namespace from default to that particular namespace.
> - Whenever you issue a `kubectl` command you are targeting the default namespace unless specified otherwise. 
> - If you want it to look into another namespace use `-n namespace`

> [!caution]- Somethings cannot be namespaced such as nodes, persistent volume etc.

### Use cases of Namespaces
- *Allowing teams or projects to exist in their own virtual clusters* without fear of impacting each other’s work.
- *Enhancing role-based access controls (RBAC)* by limiting users and processes to certain namespaces.
- Only certain users can push to production
	- ![[attachments/Pasted image 20220828103737.png]]
- Enabling the dividing of a cluster’s resources between multiple teams and users via resource quotas.
- Providing an easy method of separating development, testing, and deployment of containerised applications enabling the entire lifecycle to take place on the same cluster.