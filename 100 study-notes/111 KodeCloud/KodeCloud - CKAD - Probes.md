---
created: 2022-09-23 17:15
updated: 2022-09-26 13:28
---
---
**Links**: [[111 KodeCloud Index]]

---
## Lifecycle of a pod
### Pod Status
- A pod has a **Pod Status** and **Pod conditions**.
	- A *pod status* tells us *where the pod is in its lifecycle*.
- When a pod is first created it is in a **pending state**.
	- This is where the *scheduler tries to figure out where to place the pod*.
	- If the scheduler cannot find where to place the pod then it remains in the pending state.
	- To find why its in the pending state run: `k describe pod/<pod-name>`
- Once the pod is scheduled it goes into the **ContainerCreating** status.
	- This is where the images for the pods are pulled and the container starts.
- Once the container starts the pod goes into the **Running** state.
	- It continues in this state until the program completes successfully or is terminated.

> [!important]- At any point in time in a pod's lifecycle its status can be *only one of these values*.
> It only gives high level summary of a pod.

### Pod Conditions
- Pod conditions complement pod status.
- *It is an array of true or false values that tell us the state of the pod*.
	- ![[attachments/Pasted image 20220926120848.png]]
- When a pod is scheduled on a node the *PodScheduled* condition is set to true.
- When a pod is *initialised* its value is set to true.
- If a pod has multiple containers and all the containers are ready then *ContainersReady* condition is set to true.
- Finally the pod itself is considered to be *Ready*.

- Get the pod conditions: `k describe pod/<pod-name>`
	- ![[attachments/Pasted image 20220926120958.png]]
- We can also see the the Ready state of the pod in the output of `k get pods`
	- ![[attachments/Pasted image 20220926121040.png]]

## Readiness Probes
- We are interested in *Ready condition*.
	- It means that application inside the pod is ready and is ready to accept user traffic.
- Different containers may take different time to be up depending on the application they are running.
	- During this period the pod continues to show that the application is running which is not exactly true.
- **When a service is attached to a pod it depends on a pod's ready condition to route traffic to it**.

> [!note]- *By default k8s assumes that when a container is created it is ready to serve user traffic*.
> - It sets the value of Ready condition of each container to true.

- But if the container took longer to get ready and traffic is sent to it by the service, users will get an error.
	- We need a way to *tie the the Ready condition to the actual state of the application inside the container*.

- There are different ways in which we can set probes to determine if the application is ready or not.
	- For a web application it can be an HTTP test like `/api/ready`
	- In case of database it can be a simple TCP test
	- Or *execute a simple command* within the container to run a custom script that would exit successfully if the application is ready.

- Sample yaml file with readiness probe
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
	  readinessProbe:
	    httpGet:
		  path: /api/ready
		  port: 8080
```

- Now when the container is created k8s doesn't immediately set the ready condition to true instead it performs a test to see if the API responds positively.
	- Until then the service does not forward any traffic to the pod.

- Different ways of configuring the readiness probe
	- ![[attachments/Pasted image 20220926123609.png]]
	- If we know our application will take 10s to warm up we can add an initial delay to the probe.
	- We can specify how often to probe.
- *By default if an application is not ready after **3** attempts the probe will stop*.
	- We can choose to make more attempts by increasing the failure threshold.

- Scenario: You have 2 pods which are serving application. 
	- The container takes some time to be ready.
	- Now you choose to scale the deployment to 3.
	- If you don't have any kind of readiness probe then the service will start sending the traffic to the container which is not ready.
	- Some application users will face down time.
	- This can be avoided if we use readiness probe because now the pod will only be added to service only if readiness probe is true.

## Liveness Probe
- When an *application in a container crashes k8s restarts the the container*. 
- We can have a scenario where the application stops working (freezes) but the container continues to stay alive.
	- May be due to a bug in the code the application is stuck in infinite loop.
- As far as k8s is concerned the container is up so the application is assumed to be up.
	- We need to restart the container. 
- This is where liveness probe can help us.

> [!note]- *A liveness probe can be configured on the container to periodically test whether application within the container is healthy*.
> If the test fails the container is considered unhealthy, it is destroyed and a new container is spun up.

- As a developer we get to define what is means for the application to be ready.
	- In case of web application it can be an api server
	- In case of database we can test a particular TCP socket to see if it is listening.
	- Or we can execute a command to perform a simple test.

- Liveness probe is configured in the same way as the readiness probe.
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
	  livenessProbe:
	    httpGet:
		  path: /api/heal
		  port: 8080
```

- Liveness probe options
	- We have the same options as readiness probe
	- ![[attachments/Pasted image 20220926130603.png]]

> [!caution]- Readiness and Liveness probes are *added at the container level* so that we can have different probes for different containers.
