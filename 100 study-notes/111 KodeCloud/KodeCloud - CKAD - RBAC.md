---
created: 2022-10-08 19:31
updated: 2023-05-11 13:33
---
---
**Links**: [[111 KodeCloud Index]]

---
## RBAC
- **We create a role by creating a role object**.
- Each rule has 3 sections
	- `apiGroups`: *For the core group we can leave the `apiGroups` blank. For any other group we specify the group name*.
	- `resources`: We can restrict access to only particular resources by specifying the `resourceNames` field.
		- ![[attachments/Pasted image 20221008194459.png]]
	- `verbs`: actions the users of these roles can take.

> [!note]- We can have **multiple rules for a single role**.

- Simple *RBAC example*
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	namespace: default
	name: developer-rbac
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

- Simple *Role Binding example for a user*
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
	namespace: default
	name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user # name of the user or group
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer # name of the role
  apiGroup: rbac.authorization.k8s.io
```

- In the above example `subjects -> kind` will be different for groups and service accounts.
	- For service accounts: `kind: ServiceAccount`
	- For groups: `kind: Group`

> [!caution]- **Roles and RoleBindings are namespaced objects**.
> In the above example the dev-user gets access to pods **only within the default namespace**.
> *If we don't specify the namespace they are created in the default namespace*.
> ![[attachments/Pasted image 20221008202019.png]]

- View the roles: `k get roles`
- View the role bindings: `k get rolebindings`

- **Checking if as a user we have access to a particular resource in a cluster**
	- `k auth can-i create deployments` or `k auth can-i delete pods`
- If we are an administrator then we can **impersonate another user** to check their permissions.
	- `k auth can-i create pods --as <user-name>`
- If instead of users we want to check the access of service accounts we can use
	- `k auth can-i create pods --as system:serviceaccount:<namespace>:<service-account-name>`
- We can also specify the namespace using
	- `k auth can-i create pods --as <user-name> --namespace test`