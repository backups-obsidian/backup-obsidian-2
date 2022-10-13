---
created: 2022-10-03 15:58
updated: 2022-10-13 11:45
---
---
**Links**: [[111 KodeCloud Index]]

---
## Security Primitives
- In terms of security controlling access to the kube-apiserver is the first line of defence.
- We need to make 2 kinds of decisions.
	- *Who* can access the server? -> Defined by **authentication** mechanisms
		- There are different ways of authenticating to the cluster.
	- *What* can they do? -> **RBAC authorisation**

- All communication between the kube-apiserver and other components of k8s is encrypted using TLS certificates.
	- ![[attachments/Pasted image 20221003155826.png]]
- *By default all pods in the cluster can access each other (using pod IP)*. 
	- We can restrict access using [[KodeCloud - CKAD - Network Policies | Network Policies]]

## Authentication
- We can have different users like admin, developers and machines. 
- k8s does not manage user accounts natively. It relies on an external source.
	- *We cannot create users in k8s or view the users in k8s*.
	- **But we can manage and create service accounts**.

> [!note]- All user access is managed by the kuber-apiserver.
> Whether we are using `kubectl` or the API directly. All the requests go through the kubeapi-server.
> kubeapi-server authenticates the request before processing it.
> ![[attachments/Pasted image 20221003160444.png]]

- Different authentication mechanisms:
	- ![[attachments/Pasted image 20221003160524.png]]
