---
created: 2022-10-03 16:26
updated: 2022-10-03 22:10
---
---
**Links**: [[111 KodeCloud Index]]

---
- Using certificate and key for making a curl request

```bash
curl https://my-kube-playground:6443/api/v1/pods \
	--key admin.key
	--cert admin.crt
	--cacert ca.crt
```

- We can specify the same information using the `kubectl` command 

```bash
kubectl get pods
	--server my-kube-playground:6443
	--certificate-authority ca.crt
	--client-key admin.key
	--client-certificate admin.crt
```

- Typing these in everytime is a tedious task so we move it to a kubeconfig file and then specify it while running the commands
	- `k get pods --kubeconfig config`

> [!note]- By default the kubeconfig looks for a file at `~/.kube/config`
> So if we have a kubeconfig file here named `config` we don't have to specify it everytime.

- The kubeconfig file is in a specific format. It contains 3 sections: Clusters, Users & Contexts
	- Context defines which user account will be used to access which cluster.
	- ![[attachments/Pasted image 20221003162803.png]]

- Sample kubeconfig file
	- ![[attachments/Pasted image 20221003163057.png]]
- Similarly we can add other clusters 
	- ![[attachments/Pasted image 20221003163604.png]]

- Contexts need not be of the format `<user>@<cluster>` they can be simple words also. It is upto us what we want to name them.

> [!question]- How does `kubectl` know which context to choose from?
> - We can specify the default context by adding it in the kubeconfig file under `current-context`  

- View the current kubeconfig file: `k config view`
	- If we don't specify which file we want to use it will end up using the kubeconfig at the default location.
- Viewing a specific kubeconfig file: `k config view --kubeconfig=myconfig`
- We can change the current context using: 
	- `k config use-context <context>`

> [!note]- Changes made by the kubectl actually reflects in the kubeconfig file.

- In each cluster we may have different namespaces. **We can configure a context to switch to a particular namespace**.
	- The context section in the kubeconfig file can take additional field called namespace.
	- So when we switch to that context we will automatically be in that namespace.
	- ![[attachments/Pasted image 20221003164404.png]]

- We should provide full path to the certificate. 
- *Optionally we can directly specify the contents of certificate in the kubeconfig file after **base64 encoding** it*.
	- ![[attachments/Pasted image 20221003164648.png]]

