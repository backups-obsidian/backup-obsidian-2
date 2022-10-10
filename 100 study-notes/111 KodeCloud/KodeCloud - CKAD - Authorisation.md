---
created: 2022-09-15 21:09
updated: 2022-10-08 19:50
---
---
**Links**: [[111 KodeCloud Index]]

---
## Why Authorisation
- There will be other users accessing the cluster and we will be creating accounts for them.
	- We don't want all of them to have the same level of access as us.
	- We want to provide everyone the minimum level of access.
- *We partition the cluster using namespaces and we want to restrict a user's access to its namespace*.

## Different types of authorisation
- *Node Authoriser*: 
	- Access within the cluster
	- Used by nodes
- *ABAC*: Attribute based access control. 
	- Difficult to manage.
	- Each user gets its own set of permissions
		- ![[attachments/Pasted image 20221008191533.png]]
- *RBAC*: Role based access control.
	- **We define roles (set of permissions)** for example developer
	- Then we associate all the developers to that role
	- Now when a change has to be made to a user's access we simple modify the role by adding or removing permissions.
	- RBAC provide a more standard approach to managing access within the k8s cluster.
	- ![[attachments/Pasted image 20221008191430.png]]
- Webhook:
	- *Manage all the authorisation externally*. 
	- For example Open Policy Agent.
	- ![[attachments/Pasted image 20221008191707.png]]
- **AlwaysAllow**: Allows all the requests without performing any authorisation checks
- **AlwaysDeny**: Deny all requests.

---

- Authorisation modes are set using the `authorization_mode` option in the kubeapi-server.
	- We can check the authorization mode of our cluster using `kubectl describe pod kube-apiserver-controlplane -n kube-system | grep auth`
- If we don't specify this option it is *set to AlwaysAllow by default*
	- ![[attachments/Pasted image 20221008192435.png]]
- We can have multiple modes in the kubeapi-server.
	- The requests are authorised by each one in the order they are specified.
- In the below example when user sends a request the authorisation is first handled by the node authoriser.
	- *Now since the node authoriser only handles node request it DENIES the request*.
	- When a mode denies the request it is forwarded to the next one in the chain.
	- As soon as *one of the modes approves the request* no more further checks are performed and the user is granted permission.
	- ![[attachments/Pasted image 20221008192840.png]]