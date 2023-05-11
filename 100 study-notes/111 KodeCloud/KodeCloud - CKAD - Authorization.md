---
created: 2022-09-15 21:09
updated: 2023-05-11 13:46
---
---
**Links**: [[111 KodeCloud Index]]

---
## Why Authorization
- There will be other users accessing the cluster and we will be creating accounts for them.
	- We don't want all of them to have the same level of access as us.
	- We want to provide everyone the minimum level of access.
- *We partition the cluster using namespaces and we want to restrict a user's access to its namespace*.

> [!note] Authorization is a step that comes after authentication is successful.

## Different types of authorization
- *Node Authorizer*: 
	- Access within the cluster
	- Used by nodes
- *ABAC*: Attribute based access control. 
	- Difficult to manage.
	- Each user gets its own set of permissions
		- ![[attachments/Pasted image 20221008191533.png]]
- **RBAC**: Role based access control.
	- **We define roles (set of permissions)** for example developer
	- Then we associate all the developers to that role
	- Now when a change has to be made to a user's access we simple modify the role by adding or removing permissions.
	- *RBAC provides a more standard approach to managing access within the k8s cluster*.
	- ![[attachments/Pasted image 20221008191430.png]]
- Webhook:
	- *Manage all the authorisation externally*. 
	- For example Open Policy Agent.
	- ![[attachments/Pasted image 20221008191707.png]]


## Authorization Modes
- Authorization modes are set using the `authorization_mode` option in the kubeapi-server.
	- We can check the authorization mode of our cluster using `kubectl describe pod kube-apiserver-controlplane -n kube-system | grep auth`
	- The name of the kube-apiserver pod might differ depending on the way k8s has been setup. Find the api-server pod using the command `k get pods -n kube-system`.
- If we DON'T specify this option while starting the api-server it is **set to AlwaysAllow by default**
	- ![[attachments/Pasted image 20221008192435.png]]
	- **AlwaysAllow**: Allows all the requests without performing any authorization checks
	- **AlwaysDeny**: Deny all requests.
- We can have *multiple modes* in the kubeapi-server.
	- The requests are **authorized by each one** in the order they are specified.
- In the below example:
	- When user sends a request the authorization is *first handled by the node authorizer*.
	- *Now since the node authorizer only handles node request it DENIES the request*.
	- **When a mode denies the request it is forwarded to the next one in the chain**.
	- **As soon as one of the modes approves the request no more further checks are performed and the user is granted permission**.
	- ![[attachments/Pasted image 20221008192840.png]]

