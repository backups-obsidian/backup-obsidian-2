---
created: 2022-09-15 21:09
updated: 2022-10-08 20:44
---
---
**Links**: [[111 KodeCloud Index]]

---
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

> [!caution]+ We can create a clusterrole for a namespaced resource as well.
> **When we do that the user will have access to these resources across all namespaces**.
> If we create a normal role then the user will have access to resources in that namespace only.

- K8s creates some cluster roles by default.

