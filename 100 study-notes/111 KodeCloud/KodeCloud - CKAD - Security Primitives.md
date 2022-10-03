---
created: 2022-10-03 15:58
updated: 2022-10-03 16:00
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


### Authentication
- We can different users like admin, developers and machines. 
- k8s doe