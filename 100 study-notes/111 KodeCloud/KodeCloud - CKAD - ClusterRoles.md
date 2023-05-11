---
created: 2022-09-15 21:09
updated: 2023-05-11 13:10
---
---
**Links**: [[111 KodeCloud Index]]

---
## Cluster Roles
> [!note]- There are two kinds of resources: **cluster scoped** and **namespaced**.
> ![[attachments/Pasted image 20221008202138.png]]
> - This is not a comprehensive list. 
> - To list all the namespaced resources we can use `k api-resources --namespaced=true` 
> - We can use `k api-resources --namespace=false` to view cluster scoped resources.

- *We use roles and role bindings to authorise users to namespaced based resources*.
- **We use clusterrole and clusterrolebindings to authorise users to cluster scoped resources**.
	- clusterroles are like normal roles but they are created for the whole cluster.
	- clusterroles are cluster wide and *not part of any namespace*.

- Sample yaml file for creating a cluster role

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
	name: cluster-administrator
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list", "get", "get", "create", "delete"]
```

- Next we link the user to the cluster role by creating a cluster role binding.
- Sample yaml file for creating a cluster role binding

```yaml
apiVersion:rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

### Cluster Roles and Cluster Role Bindings for namespaced resources
- Suppose we want a *user to list pods in some namespaces* then one way would be to create a role and role binding in those namespaces.
	- This is a tedious process if we have a lot namespaces.
	- Instead we can **create a single Cluster Role and then create Role Bindings in namespaces where we want the user to list the pods**.
- Suppose we want a *user to list pods in **all** namespaces* then one way would be to create a role and role binding in all the namespaces.
	- Or another way is creating single cluster role and creating a role binding in all the namespaces.
	- Instead we can **create a single Cluster Role and a single Cluster Role Binding to allow the user to list the pods in all the namespaces**.
