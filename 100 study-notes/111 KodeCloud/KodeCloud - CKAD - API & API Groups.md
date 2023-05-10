---
created: 2022-09-15 21:09
updated: 2023-05-09 17:29
---
---
**Links**: [[111 KodeCloud Index]]
**Recommended Reads**: [[KodeCloud - CKAD - Service Accounts | Service Accounts]]

---
## API & API Groups
- When we use either cURL (REST) or kubectl we are interacting with the kubeapi-server.
	- If we want to see the http requests that are being fired when we use kubectl then we can use the verbose flag.
	- `k get pods -v 6`
		- ![[attachments/Pasted image 20230509123832.png]]
- Kubernetes API is grouped into **multiple groups**.
	- ![[attachments/Pasted image 20221008184849.png]]
	- `/api` & `/apis` are responsible for the cluster functionality
	- We can list all the api groups using `k api-resources`
- `/api`: **core group**
	- The `/api` endpoint is *legacy* and used only for core resources (pods, secrets, configmaps, etc.). 
	- ![[attachments/Pasted image 20221008184401.png]]
- `/apis`: **named group**
	- These are more organised and *going forward all the newer features will be made available through these named groups*.
	- ![[attachments/Pasted image 20221008185143.png]]
- We can list all available api groups by using: 
	- `curl -k https://localhost:6443`(from inside the control plane/master node) or using `curl -k https://<ip-or-hostname-controlplane>:6443`.
		- We can find the IP/hostname of the control plane by looking into the kubeconfig file or using the command `kubectl cluster-info`.
		- Here `-k` in curl stands for insecure flag.
	- *Mostly we won't be allowed access to the cluster using curl (except `/version`) since we haven't provided any authentication mechanisms*.
		- ![[attachments/Pasted image 20230509124427.png]]
	- We can authenticate by passing the client key and the client certificate files with the curl command.
		- ![[attachments/Pasted image 20221008185937.png]]
- An alternate option is to **start a kubectl proxy client**. 
	- `kubectl proxy` command launches a proxy on `localhost:8001` and uses credentials from the kubeconfig file to forward the request to the kubeapi-server.
		- Now we don't have to specify them in the curl command.
	- We can access the kubectl proxy service using `curl http://localhost:8001/ -k`.
	- Remember *kube proxy is different from kubectl proxy*.
- Checking the version of the cluster using API:
	- Setting up the proxy: `k proxy` and then `curl localhost:8001/version`

> [!note]- There are 2 different ways of authenticating while using curl
> - **Normal users**: using *client key* and *client certificate*.
> ![[attachments/Pasted image 20230509171919.png]]
> - **Service accounts**: using *authorization bearer token*.
> ![[attachments/Pasted image 20230509171953.png]]
> - Accessing the API from *inside the container*
> ![[attachments/Pasted image 20230509172524.png]]

## References
- **Must Read**: How to use the API: [How To Call Kubernetes API using Simple HTTP Client (iximiuz.com)](https://iximiuz.com/en/posts/kubernetes-api-call-simple-http-client/)
	- Describes how to use the k8s API as users as well as service accounts.