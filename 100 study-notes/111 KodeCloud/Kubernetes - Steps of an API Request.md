---
created: 2023-05-09 12:57
updated: 2023-05-11 15:09
---
---
**Links**: [[111 KodeCloud Index]]
**Recommended Reads**: [[KodeCloud - CKAD - Admission Controllers | Admission Controllers]]

---
## Steps of an API Request
- Details on what happens when we submit a request to the k8s API:
	- ![[attachments/Pasted image 20230510130038.png]]
	- When we do a `k apply` command then the yaml file is converted to JSON by kubectl and then sent to the API server.
	- The first component of the API server is the **API HTTP Handler**.
		- It is an HTTP server listening for requests.
	- The second component of the API server is **Authentication**.
		- It *checks if the user or the service account should be allowed to access the cluster*.
	- The third component of the API server is **Authorization** (RBAC).
		- This checks if the user or the service account can list, delete, create resources in the cluster.
		- **This is where RBAC rules are evaluated**.
	- The fourth component of the API server is **Mutation Admission Controller**.
		- It looks at our YAML and modifies/mutates it.
		- For example if we forget to add the default storage class to our yaml then the mutation admission controller will add it.
	- The fifth component of the API server is **Schema Validation**.
		- It validates if the resource we have sent has a valid schema. Basically checking if the yaml is valid or not.
	- The sixth component of the API server is **Validation Admission Controller**.
		- It comes in when we try to deploy a pod in a namespace that DOES NOT exist.
- To read more on validating or mutating admission controller read [[KodeCloud - CKAD - Admission Controllers#Admission Controllers | this]]. 

> [!note] If we manage to pass the Validation Admission Controller then our yaml file is saved in etcd.

- Examples of custom admission controllers:
	- ![[attachments/Pasted image 20230510130301.png]]

## References
- [Kubernetes API Explained - YouTube](https://www.youtube.com/watch?v=aTFmtac2wCg)