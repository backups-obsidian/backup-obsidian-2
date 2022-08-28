---
created: 2022-08-11 19:51
updated: 2022-08-28 10:20
---
---
**Links**: [[110 Kubernetes Index]]

---

## Pods & Containers
- **Pod is the smallest unit** that we will interact with. 
- Pod is basically a *wrapper for a container*. 
- In *each worker node we can have multiple pods and we can have multiple containers inside a single pod*. 

> [!note]- Generally we have *1 pod per application* i.e. *1 container inside a pod* (this is the most common use case). 
> If we have any helper containers for the main application then we tend to have more than one container per pod.

- All containers inside the same pod *share volumes and IP addresses*
	- ![[attachments/Pasted image 20220828094836.png]]

- The **virtual network assigns each pod its own IP address**. 
	- So each pod is its own self contained server with its own IP address. 
	- The pods communicate with each other via internal IP addresses.
	- ![[attachments/Pasted image 20220828095045.png]]

> [!important]- **We don't create containers inside Kubernetes we only create pods**. 
> - Pods are an abstraction layer over containers provided by Kubernetes. 
> - A pod manages the container running inside it without our intervention. 
> - For example if a container stops or dies inside a pod it will be automatically restarted.

## Services
- Pods also die and have to be restarted. *When pods are restarted they are assigned a new IP address*. 
	- Now if your application is talking to the database and the database server dies and is restarted again it will be assigned a new IP address. 
	- Now it becomes very inconvenient to adjust that IP address. 
	- So **because of this another component of Kubernetes called service is used**. 
	- Now if the pod behind a service dies a new pod is created and attached to the same service.
- Service is an *alternative to IP addresses*. 
- Instead of having dynamic IP addresses there are services *sitting in front of each pod that talk to each other*. 
	- ![[attachments/Pasted image 20220828095730.png]]
- It **also acts as a load balancer** which means it will catch the request and forward it to the pod which is least busy.
	- ![[attachments/Pasted image 20220828095747.png]]

> [!note]- Service servers two main purposes → **permanent IP addresses** and **load balancers**.

## Ingress
### Without ingress. 
- When you use an *LoadBalancer service* for your deployment you get an external ip address and port to access your application. 
- We have to **use an external service**.
	- ![[attachments/Pasted image 20220828100144.png]]
- Although it is accessible to everyone it is not convenient to access it via ip:port. We would prefer a domain name instead.
	
### With ingress. 
- While using ingress **instead of an external service we will have an internal service**.
- We will not expose our application via IP address and port.
	- ![[attachments/Pasted image 20220828100216.png]]
	- ![[attachments/Pasted image 20220828100343.png]]

## ConfigMap
- **ConfigMap is external configuration for your application.**

> [!example]- ConfigMap usage
> - Suppose you have several database pods in a database deployment with a *service name of mongo-database-service*. 
> - Now the pods in the application deployment use this service name when communicating with the database. 
> - Now **if you change the service name to mongo-service then you will have to rebuild the image for your application since the service name parameter is defined in the code of the application**.
> - This is where using config maps with pods can help us. 
> - ![[attachments/Pasted image 20220828101034.png]]

- ConfigMap is for *storing non confidential data only*. 

> [!caution]- We should not store things like database username and password in it.

## Secret
- Just like ConfigMap secrets are *also used for external configuration for your application*.
- Secret is just like config map but the main difference is that it is used to **store secret data like database username and password which we could not store in the ConfigMap**. 

> [!note]- The values **must be base64 encoded** before adding them to the yaml file.
- Secret components are *meant to be secured using third party tools in k8s*.

## Volumes
- It is used to persist data and is *generally connected to the database deployment*. 
- So if any of the pods go down they won’t wipe out any data with them since data is not persisted in pods. 
- **Volumes can be external or local.**
	- ![[attachments/Pasted image 20220828101401.png]]
- *Kubernetes explicitly won't manage any persistent storage that you have*. 
	- This means *you are responsible for backing up, replicating the data and making sure it is kept on a proper hardware*.

## Deployment 
- *In Kubernetes generally you won't be interacting with individual pods but deployment yaml file*. 

> [!note]- `Deployment` component is an abstraction on top of pods.

## StatefulSet
> [!caution]- *Deployment component is for stateLESS apps*, **statefulset component is for stateFUL apps**.

> [!question]- Why deployments cannot be used to manage pods containing databases?
> - You can easily create replicas of pods hosting applications but that is not possible in case of database pods since if there are multiple database pods then **there can be write and read inconsistencies**.
> - ![[attachments/Pasted image 20220828101937.png]]

- **Statefulset component is specifically meant for databases**. 
	- It will make sure that reads and writes are synchronised. 
	
- But managing databases using statefulset is not easy. 

> [!danger]- Hence DB are generally hosted outside Kubernetes cluster and *just have stateless applications in Kubernetes*.