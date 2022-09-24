---
created: 2022-09-15 21:09
updated: 2022-09-24 15:52
---
---
**Links**: [[111 KodeCloud Index]]

---
## Service Accounts
- There are 2 types of accounts in k8s:
	- *User Account*: used by humans (Admins & Developers).
	- *Service Account*: used by machines (prometheus or Jenkins).

- Scenario: We have created a dashboard application to list the pods in a k8s cluster. 
- To get the pods it needs to query the kubeapi server and for that it needs to be authenticated.
- This is done using a service account.

- Creating a service account: `k create serviceaccount <service-account-name>`
- List all the service accounts: `k get serviceaccount`
- When a service account is created it *creates a token automatically*.
	- **Token is what must be used by the external application to authenticate with the k8s api**.
	- The *token is created as a secret*.
	- ![[attachments/Pasted image 20220924143626.png]]

- This *token can be used as an Authentication Bearer token* while making a rest call to the k8s api.

> [!important]- So we *create a service account*, **attach it RBAC** (role based access controls) and then *export the service account tokens* to use with the 3rd party application.

- In case the *application* is *hosted inside the k8s cluster* there is NO need to export the service account token since *the secret can be mounted as a volume inside the pod*.
	- For this we just need to specify the service account value under the `spec` section of the pod definition file.
	- Parameter: `serviceAccountName`

- **In every namespace k8s creates a service account named default**.
	- Whenever a pod is created *the default service account's token is automatically mounted as a volume on the pod*.
	- Default service account is automatically mounted to each and every pod unless we specify `automountServiceAccountToken: false` under `spec` of *pod definition file*.
	- The default service account is very restrictive in nature and can only query few of k8s apis.
	- ![[attachments/Pasted image 20220924151528.png]]

- Now from inside the pod if we list the contents of the mount directory we will see the secret mounted as 3 separate files.
	- ![[attachments/Pasted image 20220924151640.png]]
- **If we want to use a different service account, we can specify it in the pod definition and its secret token will be mounted in the pod**.
	- ![[attachments/Pasted image 20220924151921.png]]
- Whenever service accounts are changed the pods must be deleted and recreated.
- The short form of service accounts is `sa`
- We can get the service account mounted to a pod using: `k get pod/<pod-name> -o yaml`

> [!danger]+ Doubtful on what happens when you include an SA to the pod definition file. 
> - Are the secrets of that SA automatically mounted or do we mount it?