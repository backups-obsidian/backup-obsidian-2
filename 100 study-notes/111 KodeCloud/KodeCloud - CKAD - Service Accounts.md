---
created: 2022-09-15 21:09
updated: 2023-05-03 14:45
---
---
**Links**: [[111 KodeCloud Index]]

---
## Service Accounts
- There are 2 types of accounts in k8s:
	- **User Account**: used by humans (Admins & Developers).
		- Kubernetes *has no APIs to create users and groups*.
		- Users and groups are managed externally.
	- **Service Account**: used by machines (prometheus or Jenkins).
		- K8s has APIs to create service accounts.
- Service accounts are created in a namespace so they are **namespaced objects**.

> [!question]- What do we need service accounts?
> - Imagine we have created a dashboard application to list the pods in a k8s cluster. 
> - To get the pods it needs to query the kube-api-server and for that it needs to be authenticated.
> - This is done using a service account.

- When a service account is created it *creates a token automatically* (before v1.24).
	- **Token is what must be used by the external application to authenticate with the k8s api**.
	- The *token is a JWT token created as a k8s secret*.
		- Since it is a k8s secret it is base64 encoded.
	- ![[attachments/Pasted image 20220924143626.png]]
- This *token can be used as an Authentication Bearer token* while making a rest call to the k8s api.

> [!important]- So we *create a service account*, **attach it RBAC** (role based access controls) and then *export the service account tokens* to use with the 3rd party application.

- In case the *application* is *hosted inside the k8s cluster* there is NO need to export the service account token since *the secret is mounted as a volume inside the pod*.
	- For this we just need to specify the service account value under the `spec` section of the pod definition file.
	- They are specific to a pod and not container.
	- Parameter: **`serviceAccountName`**
- **In every namespace k8s creates a service account named default**.
	- Whenever a pod is created **the default service account's token is automatically mounted as a volume on the pod**.
	- Default service account *is automatically mounted to each and every pod* unless we specify `automountServiceAccountToken: false` under `spec` of *pod definition file*.
	- The default service account is very restrictive in nature and can only query few of k8s apis.
	- ![[attachments/Pasted image 20220924151528.png]]
- If we want a more permissive service account then we should create a new service account and attach roles to it using role bindings.
	- We should not increase the permissions of the default service account since they  are by default mounted to all the containers. 
	- It would be a huge security risk.
- Now from inside the pod if we list the contents of the mount directory we will see the secret mounted as 3 separate files.
	- ![[attachments/Pasted image 20220924151640.png]]
	- Here `namespace` contains the namespace name, `token` contains the secret (JWT token for authentication) and `ca.crt` is the public certificate of the kube-api-server.
- **If we want to use a different service account, we can specify it in the pod definition and its secret token will be mounted in the pod** at `/var/run/secrets/kubernetes.io/serviceaccount/token`.
	- ![[attachments/Pasted image 20220924151921.png]]
- If we try to list the pods using the k8s api from inside the pod using the *default service account* or a service account that does not have the required roles, we will get an error.
	- Without using the token: anonymous user
		- ![[attachments/Pasted image 20230503135854.png]]
	- Using the token but with insufficient permissions:
		- ![[attachments/Pasted image 20230503140317.png]]
		- The service account used in the example doesn't have any roles attached to it.
- **Whenever service accounts are changed the pods must be deleted and recreated**.
- The short form of service accounts is `sa`
- We can get the service account mounted to a pod using: `k get pod/<pod-name> -o yaml`
- *Each service account can belong to one or more groups*.
	- Groups are used to grant permissions to multiple accounts at the same time.

## Change after 1.24
- With Kubernetes **v1.24**, non-expiring service account tokens are no longer auto-generated.
- In previous versions when a service account was created a token was created automatically and this token had no expiration time.
- In more recent versions after v1.24, API credentials are obtained directly by using the [TokenRequest](https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/) API, and are mounted into Pods using a **[projected volume](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume)**. 
- The tokens obtained using this method have *bounded lifetimes*, and are *automatically invalidated when the Pod they are mounted into is deleted*.
- For most users, this change will be transparent and *DOES NOT require any intervention*.

> [!note]- *Service account tokens* are also completely **valid to use outside of the cluster to authenticate to the API server**. 
> - If you use service account tokens in this way, you will have to make a small change to your workflow with v1.24: **explicitly requesting a service account token secret to be generated**.
> - ![[attachments/Pasted image 20230503144354.png]]
> ---
> [Kubernetes 1 24 - ServiceAccounts and secrets - YouTube](https://www.youtube.com/watch?v=vk0EIznJJe0)

### References
- [Service Account Tokens in Kubernetes v1.24 | D2iQ Engineering](https://eng.d2iq.com/blog/service-account-tokens-in-kubernetes-v1.24/)

## Commands
- Creating a service account: `k create serviceaccount <service-account-name>`
- List all the service accounts: `k get serviceaccount`

## References
- [What are Kubernetes Service Accounts? - YouTube](https://www.youtube.com/watch?v=uoLXbrGdLRE)