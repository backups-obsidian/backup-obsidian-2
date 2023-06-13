---
created: 2023-06-13 12:13
updated: 2023-06-13 12:38
---
---
**Links**: [[111 KodeCloud Index]]

---
## Port Forwarding vs Proxy
- `kubectl proxy` is generally used for exposing the k8s [[KodeCloud - CKAD - API & API Groups | API]].
	- *It works at the application level (layer 7) hence it can handle only HTTP & HTTPs traffic*.
 
> [!note]- One of the most common uses of `kubectl proxy` command is to pair it with the k8s dashboard.
> Ideally we should use prometheus for monitoring. 

- **Compared to `kubectl proxy`, `kubectl port-forward` is more generic as it can forward TCP traffic while `kubectl proxy` can only forward HTTP/HTTPS traffic**. 
	- It *works at transport layer (layer 4) of the OSI model hence it can work on all the protocols based on TCP*.
	- UDP is NOT supported.
- The port-forward command, *Forwards one (or more) local ports to a pod*.
	- `k port-forward <pod-name> <port-number>`
- *Example*: Below is the name of the pod and it will forward it's port `6379` to `localhost:6379`. *All the examples do the same thing*.
	- `kubectl port-forward redis-master-765d459796-258hz 6379:6379 `
	- `kubectl port-forward pods/redis-master-765d459796-258hz 6379:6379`
	- In the below examples the pod is selected by the deployment, replicaset and service respectively 
		- `kubectl port-forward deployment/redis-master 6379:6379`
		- `kubectl port-forward rs/redis-master 6379:6379`
		- `kubectl port-forward svc/redis-master 6379:6379`

### More usage examples
- Listen on port 8888 locally, forwarding to 5000 in the pod
	- `kubectl port-forward pod/mypod 8888:5000`
- Listen on port 8888 **on all addresses**, forwarding to 5000 in the pod
	- `kubectl port-forward --address 0.0.0.0 pod/mypod 8888:5000`
- Listen on a random port locally, forwarding to 5000 in the pod
	- `kubectl port-forward pod/mypod :5000`
- Listen on port 8888 on localhost and selected IP, forwarding to 5000 in the pod
	- `kubectl port-forward --address localhost,10.19.21.23 pod/mypod 8888:5000`
- Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in the pod
	- `kubectl port-forward pod/mypod 5000 6000`
- Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in a *pod selected by the deployment*.
	- `kubectl port-forward deployment/mydeployment 5000 6000`
- Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in a *pod selected by the service*.
	- `kubectl port-forward service/myservice 5000 6000`

### Using `kubefwd` tool
- **We can forward all the services in a namespace using this tool while using the service names specified in k8s**.
	- It does this by populating the `/etc/hosts` file on our system.
	- We need to *grant it sudo access since it edits our `/etc/hosts` file*.
- [txn2/kubefwd: Bulk port forwarding Kubernetes services for local development. (github.com)](https://github.com/txn2/kubefwd)

### References
- [kubernetes - How kubectl port-forward works? - Stack Overflow](https://stackoverflow.com/questions/51468491/how-kubectl-port-forward-works)
- [9 Ways to Expose an App on Amazon EKS to the Internet - YouTube](https://www.youtube.com/watch?v=ePqUq06WoLk) 
	- Watch only the first 2 ways.