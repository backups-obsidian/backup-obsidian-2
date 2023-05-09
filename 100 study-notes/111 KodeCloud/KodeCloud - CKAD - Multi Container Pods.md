---
created: 2022-09-15 21:09
updated: 2023-05-08 14:41
---
---
**Links**: [[111 KodeCloud Index]]

---
## Multi Container Pods
- There are different patterns of multi container pods in k8s:
	- **Sidecar**: Deploying a logging agent along side with web server to collect logs and forward them to a central log server.
	- **Adapter**: Before sending the logs to the central log server we want to format them to a particular format.
		- Adapter container processes the logs before sending them to the central log server.
			- ![[attachments/Pasted image 20230503162530.png]]
	- **Ambassador**: Suppose the application communicates with different databases in different stages of development. We need to modify the connectivity depending on the environment.
		- We can outsource this logic to a separate container within the pod so the application can always refer the database at localhost and the other container will proxy the request to the right database. This container is known as the ambassador container.
		- Another example: here nginx is proxying all the requests to the webapp container
			- ![[attachments/Pasted image 20230503162424.png]]
- The above were some of the patterns in designing a multi container pod. 
	- **The pod definition file remains the same in all of them**.
- In multi container pods the containers
	- Share the same *lifecycle* which means they are created together and destroyed together.
	- Share the same *network* space which means they can **refer to each other as `localhost`**.
	- *They have access to the same storage volume*.
		- ![[attachments/Pasted image 20230503161935.png]]
		- If you are having trouble understanding how volumes are being mounted, refer the following links: [[KodeCloud - CKAD - Volumes#Volumes | Volumes]] and [[KodeCloud - CKAD - Volumes#Difference between emptydir & hostpath | emptydir]]

- Sample multi container pod definition file:
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: simple-webapp
labels:
	name: simple-webapp
spec:
	containers:
	- name: simple-webapp
	  image: simple-webapp
	  ports:
	  - containerPort: 8080
	- name: log-agent
	  image: log-agent
```

## Init Containers
- In a multi-container pod, each container is expected to run a process that stays alive as long as the POD’s lifecycle. 
	- For example in the multi-container pod that we talked about earlier that has a web application and logging agent, both the containers are expected to stay alive at all times.
	- The process running in the log agent container is expected to stay alive as long as the web application is running. If any of them fails, the POD restarts.
- But at times you may want to run a process that runs to completion in a container. 
	- For example a process that pulls a code or binary from a repository that will be used by the main web application. 
	- That is a task that will be run only one time when the pod is first created. 
	- Or a process that waits for an external service or database to be up before the actual application starts. That’s where **initContainers** comes in.

- An **initContainer** is configured in a pod like all other containers, except that it is specified inside a `initContainers` section

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ;']
```

- *When a POD is first created the initContainer is run*, and the **process in the initContainer must run to a completion before the real container (hosting the application) starts**.
- You *can configure multiple such initContainers* as well, like how we did for multi-pod containers. 
	- In that case each init container is run **one at a time in sequential order**.
	- The order depends on the order specified in the YAML file.

> [!note]- If any of the *initContainers fail to complete*, Kubernetes *RESTARTS the Pod repeatedly until the Init Container succeeds*.

- Multiple initContainers sample init file
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```

## References
- [Multi-Container Pods in Kubernetes (linchpiner.github.io)](https://linchpiner.github.io/k8s-multi-container-pods.html)
	- Detailed explanation of different ways of container communication in a pod with code example.