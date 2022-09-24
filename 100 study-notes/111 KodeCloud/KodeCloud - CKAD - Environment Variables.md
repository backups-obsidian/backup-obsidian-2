---
created: 2022-09-23 18:22
updated: 2022-09-23 22:14
---
---
**Links**: [[111 KodeCloud Index]]

---
## Environment Variables
### Using `env`
- Environment variables is an array.
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: simple-webapp-color
spec:
	containers:
	- name: simple-webapp-color
	  image: simple-webapp-color
	  ports:
		- containerPort: 8080
	  env:
		- name: APP COLOR
		  value: pink
```

- **Other ways of specifying env variables are using *ConfigMaps* & *Secrets***.
- The only difference is that instead of specifying `value` we specify `valueFrom` in case of Secrets & ConfigMaps
	- ![[attachments/Pasted image 20220923182038.png]]

### Using ConfigMaps
> [!question]- What is the use of ConfigMaps if we specify `env` variables in the definition file?
> When we have a lot of pod definition files it becomes difficult to manage the environment data stored within these files.
> We can *take this configuration out of the definition files and manage it centrally using ConfigMaps*.

- There are two steps in using a ConfigMap
	- Create a ConfigMap: `k apply -f config.yaml`
	- Inject them into the pod.

- Sample ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
	name: app-config
data:
	APP_COLOR: blue
	APP_MODE: prod
```

- We can create as many ConfigMaps as we want.
- Getting the ConfigMaps: `k get configmap`
- Getting the data stored by the ConfigMaps: `k describe configmap/<name-of-map>`

- **There are 3 ways of injecting ConfigMap to a pod**.
	- ![[attachments/Pasted image 20220923184521.png]]
- In the below example we inject all the values of the ConfigMap
	- ![[attachments/Pasted image 20220923184203.png]]

- **The short form of ConfigMaps is `cm`**.

### Using Secrets
- They are used to store sensitive information.
- They are similar to ConfigMap but the values are stored in an *encoded format*.
- As with ConfigMaps there are 2 steps involved in working with secrets.
	- Create the secret
	- Inject into pods
- All the secrets must be *base64 encoded*.
	- `echo -n "some-string" | base64`
- Get the secrets: `k get secrets`
- View the attributes of the secret: `k describe secret/<secret-name>`
- We can see the base64 encoded secrets using: `k describe secret/<secret-name>`
- Decoding base64 values: `echo -n "<base64-encoded>" | base64 --decode`
- Sample secret file:
```yaml
apiVersion: vl
kind: Secret
metadata:
	name: app-secret
data:
	DB_Host: bXlzcWw=
	DB_User: cm9vdA==
	DB_Password: cGFzd3Jk
```

- **Different ways of injecting secrets in pods**:
	- ![[attachments/Pasted image 20220923210721.png]] 

- *If we mount the secret as volume then each attribute in the secret is created as a file with the value of secret as its content*.
	- ![[attachments/Pasted image 20220923215055.png]]

- The concept of safety of the Secrets is a bit confusing in Kubernetes. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. In my opinion it’s not the secret itself that is safe, it is the practices around it.
- Also the way k8s handles secrets. Such as:
	-   A secret is only sent to a node if a pod on that node requires it.
	-   Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
	-   Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

- Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, [HashiCorp Vault](https://www.vaultproject.io/).
