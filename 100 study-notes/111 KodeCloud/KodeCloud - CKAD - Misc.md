---
created: 2022-09-23 16:03
updated: 2022-10-08 20:49
---
---
**Links**: [[111 KodeCloud Index]]

---
> [!important]+ Doing dry runs
> `k apply -f dep.yaml --dry-run=client`
> This will *not create the resource*, instead, *tell you whether the resource can be created and if your command is right*.

- Output the yaml files of imperative commands
	- `k run nginx --image=nginx --dry-run=client -o yaml`
	- Other imperative commands are *create* (creating a service, creating a deployment, etc), *expose* etc.

- Get the yaml file for any resource:
	- `k get <resource-type> <resource-name> -o yaml > definition.yaml`
	- This is useful when you have already defined resources in a cluster and you don't have their yaml files.

- Formatting the output:
	1.  `-o json`: Output a JSON formatted API object.
	2.  `-o name`: Print only the resource name and nothing else.
	3.  `-o wide`: Output in the plain-text format with any additional information.
	4.  `-o yaml`: Output a YAML formatted API object.

- Forcefully apply a file 
	- `k apply -f --force <file-name>.yaml`

- Get objects from all the namespaces:
	- `k get <resource-name> -A`
	- Example: `k get pods -A`
	- Get all the objects in all namespaces: `k get all -A `

- Service full DNS: `<svc-name>.<namespace>.svc.cluster.local`
- We can remove the headers from the output of any command using: 
	- `k <command> --no-headers`

> [!caution]+ If we want to get the *short names* or *api version* or *kind* or find out *whether they are namespaced* or not we can use `k api-resources`

> [!question]- What happens when you do `k get pods`
> - The request goes to the kubeapi-server.
> - When the request hits the kubeapi-server it goes through an *authentication process and this is done using certificates*.
> - If the request was sent using kubectl then we know that the kubeconfig file has the required certificates configured.
> - After authentication the request goes through and authorisation process. This is achieved using RBAC. 
> - If the user is allowed to list the pods then he will be able to list the pods.
> ![[attachments/Pasted image 20221008204838.png]]