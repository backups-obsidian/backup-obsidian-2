---
created: 2022-10-08 19:31
updated: 2022-10-08 20:21
---
---
**Links**: [[111 KodeCloud Index]]

---
## RBAC
- **We create a role by creating a role object**.
- Each rule has 3 sections
	- `apiGroups`: *For the core group we can leave the `apiGroups` blank. For any other group we specify the group name*.
	- `resources`
		- We can restrict access to particular resources by specifying the `resourceNames` field.
		- ![[attachments/Pasted image 20221008194459.png]]
	- `verbs`: actions the users of these roles can take.

> [!note]- We can have **multiple rules for a single role**.

- Simple rbac
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	namespace: default
	name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
- apiGroups:
  resources: ["ConfigMap"]
  verbs: ["create"]
```

- After creating the role we need to *link the user to the role*.
	- For this we **create a RoleBinding object**. It links a user object to a role

- Simple role binding
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
	name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

> [!caution]- *Roles and RoleBindings fall under the scope of namespaces*.
> In the above example the dev user gets access to pods within the default namespace.
> *If we don't specify the namespace they are created in the default namespace*.
> ![[attachments/Pasted image 20221008202019.png]]

- View the roles: `k get roles`
- View the role bindings: `k get rolebindings`

- **Checking if as a user we have access to a particular resource in a cluster**
	- `k auth can-i create deployments` or `k auth can-i delete pods`
- If we are an administrator then we can impersonate another user to check their permissions.
	- `k auth can-i create pods --as <user-name>`
- We can also specify the namespace using
	- `k auth can-i create pods --as <user-name> --namespace test`