---
created: 2022-09-15 21:09
updated: 2022-09-23 16:02
---
---
**Links**: [[111 KodeCloud Index]]

---
## Namespaces
- We have been deploying resources in the `default` namespace. 
	- The `default` namespace is created by k8s. 
- k8s also creates a different pods for its internal use.
	- *To isolate this from the user, k8s creates them at `kube-system` namespace to prevent any kind of accidental deletion*.
- k8s also creates a space called `kube-public` where resources that should be available to all the users are created.
- *Each of the namespaces can have its own set of policies* to define who can do what.
- We can also *assign a quota of resources to these namespaces*.

> [!note]- The resources within the same namespace can refer to each other by just simple names.
> *If we want to connect to services in other namespace then we have to provide the full address*.
> ![[attachments/Pasted image 20220923120433.png]]
> We can do this because when a service is created a DNS entry is added to it automatically in the above format.

- **Understanding DNS service name**:
	- ![[attachments/Pasted image 20220923120649.png]]

- `k get <resources-type> -n <namespace>`
- We can have the `namespace` option under the `metadata` section in the definition file of any resource to make sure that resource gets created in that namespace.
	- ![[attachments/Pasted image 20220923144216.png]]

- We can create a namespace using the namespace definition file and then applying it.
```yaml
apiVersion: v1
kind: Namespace
metadata:
	name: dev
```

- Get all the namespaces of the system:
	- `k get ns`
- We can also create a namespace using the command line
	- `k create namespace dev`
- Changing the current namespace:
	- `k config set-context $(kubectl config current-context) --namespace=dev`
	- Since we can have different contexts (if we are managing different clusters) we first find our context (with the command in the middle) and then set its namespace to dev.
- View a resource in all namespaces
	- `k get <resource-type> --all-namespaces`
- To *limit resources in a namespace* we create a *resource quota*.
- Sample definition file for a resource quota
```yaml
apiVersion: vl
kind: ResourceQuota
metadata:
	name: compute-quota
	namespace: dev
spec:
	hard:
		pods: "10"
		requests.cpu: "4"
		requests.memory: 5Gi
		limits.cpu: "10"
		limits.memory: 10Gi
```

