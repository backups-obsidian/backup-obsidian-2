---
created: 2022-09-23 16:03
updated: 2022-10-02 22:51
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
