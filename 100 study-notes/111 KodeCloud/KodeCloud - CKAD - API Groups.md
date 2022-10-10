---
created: 2022-09-15 21:09
updated: 2022-10-08 20:42
---
---
**Links**: [[111 KodeCloud Index]]

---
## API Groups
- When we use either cURL (REST) or kubectl we are interacting with the kubeapi-server.
- Checking the version of the cluster using API:
	- `curl https://kube-master:6443/version`

- Kubernetes API is grouped into multiple groups.
	- ![[attachments/Pasted image 20221008184849.png]]
	- `/api` & `/apis` are responsible for the cluster functionality
	- We can list all the api groups using `k api-resources`
- `/api`: **core group**
	- ![[attachments/Pasted image 20221008184401.png]]
- `/apis`: **named group**
	- These are more organised and going forward all the newer features will be made available through these named groups.
	- ![[attachments/Pasted image 20221008185143.png]]
- We can list all available api groups by using: `curl -k https://master_node:6443`
	- *Mostly we won't be allowed access to the cluster using curl (except version) since we haven't provided any authentication mechanisms*.
	- We can authenticate by passing the certificate files.
	- ![[attachments/Pasted image 20221008185937.png]]

- An alternate option is to **start a kubectl proxy client**. 
	- `kubectl proxy` command launches a proxy on `localhost:8001` and uses credentials from the kubeconfig file to forward the request to the kubeapi-server.
	- Now we don't have to specify them in the curl command.
	- We can access the kubectl proxy service using `curl http://localhost:8001/ -k`
	- Remember kube proxy is different from kubectl proxy

> [!note]- All resources in k8s are grouped into different API groups.