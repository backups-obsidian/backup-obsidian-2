---
created: 2023-05-08 08:21
updated: 2023-05-08 14:29
---
---
**Links**: [[111 KodeCloud Index]]
**Recommended Reads**:
- [[Kubernetes - Objects & Resources]]

---
## Controllers 
- *Controllers are managed by k8s [[../110 Kubernetes/Kubernetes - Components of k8s & their function#^9d5e63 | controller manager]]* which is a core component of k8s.
	- Each controller *tries to move the current cluster state closer to the desired state*.
- Almost every Kubernetes object includes two nested object fields that govern the object’s configuration: the object `spec` and the object `status`.
	- The **`spec` describes the desired state** of the object, providing a description of the characteristics you want the resource to have.
	- The **`status` describes the current state of the object**, supplied and updated by the Kubernetes system and its components.
- In Kubernetes, **controllers are control loops that watch the state of your cluster, then make or request changes where needed**.
	- ![[attachments/Pasted image 20230508142255.png]]
	- In simple words controller is a program which is running on our k8s cluster and trying to *match the current state with the expected state*.
- An example where we would want to use a custom controller
	- Suppose we have a number of namespaces and we create a secret in one of the namespaces.
	- Since secrets are namespaced objects, if we want to create the same secret in all the namespaces then we will have to do it manually.
	- Instead we can create a custom controller which looks for secrets in any namespace and then creates it in all the namespaces.

## References
- [Writing Kubernetes Controllers - YouTube](https://www.youtube.com/watch?v=q7b23612pSc)