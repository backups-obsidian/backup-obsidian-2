---
created: 2023-05-29 14:06
updated: 2023-05-29 14:06
---
---
**Links**: [[111 KodeCloud Index]]

---
## Cluster Networking
- The nodes (master & worker) must have atleast one interface connected to an network.
	- The interface must have an IP address configured.
- The nodes must have a unique host name and mac address.
- The **master should accept connections on port 6443 for the API server**.
- The **kubelets** on the master and the worker node is on port **10250**.
- **Kube-scheduler** requires port **10251** to be open.
- **Kube-controllermanager** requires port **10252** to be open.
- *Worker nodes expose services* for external access so ports *30000 - 32767 should be open*.
- **Etcd** server listens on port **2379**.
- If we have multiple masters then all these ports should be open on all the masters.
	- Additionally *port 2380 should be open so that etcd clients can communicate with each other*.
- Cluster networking diagram:
	- ![[attachments/Pasted image 20230529140405.png]]
- Port requirement table:
	- ![[attachments/Pasted image 20230529140505.png]]

> [!note]- The ports mentioned must be whitelisted if we are setting up k8s networking ourselves.
> If things are not working then this is one place to look for.