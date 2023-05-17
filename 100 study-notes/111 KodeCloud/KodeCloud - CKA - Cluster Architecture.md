---
created: 2022-10-10 15:37
updated: 2023-05-17 11:44
---
---
**Links**: [[111 KodeCloud Index]]

---
## Cluster Architecture
![[../110 Kubernetes/Kubernetes - Introduction#^351f54]]

### Control Plane Components
#### kube-apiserver
- The **kube-api server** is the central hub of the Kubernetes cluster that exposes the Kubernetes API.
- *End users*, and *other cluster components*, talk to the cluster via the API server.
- So when we use *kubectl* to manage the cluster, at the backend we are actually *communicating with the API server through HTTP REST APIs*. 
	- However, the *internal cluster components* like the scheduler, controller, etc *talk to the API server using gRPC*.
- The communication between the API server and other components in the cluster *happens over TLS*.
- [[Kubernetes - Steps of an API Request in API Server | Tasks performed by the kube-api server]].

> [!note] It is the **only** component that **communicates with etcd**.

#### etcd
- etcd is an open-source **strongly consistent**, **distributed key-value store**.
- etcd uses *raft consensus algorithm* for strong consistency and availability. 
	- It works in a *leader-member* fashion for high availability and to withstand node failures.
	- [Raft (thesecretlivesofdata.com)](http://thesecretlivesofdata.com/raft/)
- etcd *stores all configurations, states, and metadata of Kubernetes objects* (pods, secrets, daemonsets, deployments, configmaps, statefulsets, etc).
- etcd allows a **client to subscribe to events using `Watch()` API** . 
	- Kubernetes *api-server uses the etcd’s watch functionality to track the change in the state of an object*.
- etcd stores all objects under the `/registry` directory key in key-value format. 
	- For example, information on a pod named Nginx in the default namespace can be found under `/registry/pods/default/nginx`.
	- Getting the first 10 items from etcd 
		- ![[attachments/Pasted image 20230516113212.png]]
- [[KodeCloud - CKA - Setting up k8s#ETCD commands | Commands for exploring etcd]]
- *Features of etcd* making it suitable for k8s
	- ![[attachments/Pasted image 20230516143551.png]]

> [!note] etcd it is the **only Statefulset component in the control plane**.

##### High Availability
- etcd cluster can use *multiple nodes for high availability*.
- There are different ways of achieving etcd HA.
	- We can use the **stacked topology** where we have *multiple master nodes and an etcd in each of them*. 
		- This is how HA setup is provisioned using kubeadm.
		- ![[attachments/Pasted image 20230517114908.png]]
	- We can use an **external etcd cluster**.
		- ![[attachments/Pasted image 20230517115000.png]] 

#### kube-scheduler
- kube-scheduler is responsible for **scheduling pods on worker nodes**.
- When we deploy a pod, we specify the pod requirements such as CPU, memory, affinity, taints or tolerations, priority, persistent volumes (PV),  etc. 
	- The *scheduler’s primary task is to identify the create request and choose the best node for a pod that satisfies the requirements*.
- A pod remains in a **PENDING** state *unless it is scheduled by the scheduler*.
- High level overview of how scheduler works:
	- ![[attachments/Pasted image 20230515150722.png]]

> [!note] kube-scheduler is a **controller** that listens to pod creation events in the API server.

##### Scheduler Working
- The scheduler has **two phases**. 
	- **Scheduling cycle** and the **Binding cycle**. 
	- Together it is called the *scheduling context*. 
	- The *scheduling cycle selects a worker node* and the *binding cycle applies that change to the cluster*.
- The first step in the creation of pods is that they end up in a **scheduling queue**.
	- This is where the pods wait to be scheduled.
- **Pods are sorted based on the priority defined on the pods** in the scheduling queue.
	- To *set a priority we must first create a priority object* and set a priority value.
		- ![[attachments/Pasted image 20221012230631.png]]
- To then choose the best node, the Kube-scheduler uses **filtering and scoring** operations.
- In the **filtering phase**, the scheduler finds the best-suited nodes where the pod can be scheduled.
	-  For example, if there are five worker nodes with resource availability to run the pod, it selects all five nodes.
	- If It is a large cluster, let’s say 100 worker nodes, and the scheduler doesn’t iterate over all the nodes. There is a scheduler configuration parameter called `percentageOfNodesToScore`.
- In the **scoring phase**, the scheduler *ranks the nodes by assigning a score to the filtered worker nodes*. 
	- The scheduler makes the scoring by calling *multiple scheduling plugins*. 
	- Finally, the *worker node with the highest rank (score) will be selected for scheduling the pod*. 
	- If *all the nodes have the same rank, a node will be selected at random*.
- Once the node is selected, the **scheduler creates a binding event in the API server**. 
	- Meaning an event to bind a pod and node.

- The scheduler has a *pluggable scheduling framework*. 
	- All of these *processes* in scheduling are **achieved using a plugin**.
		- ![[attachments/Pasted image 20221012231325.png]]

##### Customizability
- We **can create custom schedulers and run multiple schedulers in a cluster along with the native scheduler**. 
	- When we deploy a pod we can specify the custom scheduler in the pod manifest.
	- So the scheduling decisions will be taken based on the custom scheduler logic.
	- Meaning, we can *add our custom plugin to the scheduling workflow*.
- The highly customizable nature of k8s ensures that we can *write our own plugin*.
	- This is achieved using **extension points**.
	- At each stage there is an extension point to which a plugin can be plugged to.
		- ![[attachments/Pasted image 20221012231551.png]]

#### Kube Controller Manager
- [[Kubernetes - Controllers | What is a Controller?]]
- **Kube controller manager** is a *component that manages all the Kubernetes controllers*. 
	- Kubernetes *resources/objects* like pods, namespaces, jobs, replicaset are *managed by respective controllers*. 
	- Also, the **kube scheduler is also a controller** managed by Kube controller manager.
- We can extend k8s with custom controllers associated with a custom resource definition.
 
> [!note]+ **Controllers watch the kube-api server** and make required changes to achieve the desired state.
> **Controllers CANNOT watch the etcd database** because *kube-api server is the only component that can talk to the etcd database*.

#### Cloud Controller Manager
- When kubernetes is deployed in cloud environments, the cloud controller manager **acts as a bridge between Cloud Platform APIs and the Kubernetes cluster**.
- Cloud controller integration allows Kubernetes cluster to provision cloud resources like instances (for nodes), *Load Balancers* (for services), and *Storage Volumes* (for persistent volumes).
	- ![[attachments/Pasted image 20230515153802.png]]
- Cloud Controller Manager contains a set of **cloud platform-specific controllers** that ensure the desired state of cloud-specific components (nodes, Loadbalancers, storage, etc).

### Worker Node Components
#### Kubelet
- Kubelet is an agent component that **runs on every node in the cluster**. 
	- It *DOES NOT run as a container* instead **runs as a daemon service, managed by systemd**.
- It is responsible for *registering worker nodes with the API server and working with the podSpec* (Pod specification – YAML or JSON) primarily from the API server.
	- podSpec defines the containers that should run inside the pod, their resources (e.g. CPU and memory limits), and other settings such as environment variables, volumes, and labels.
- **Tasks of a kubelet**:
	- *Creating, modifying, and deleting containers for the pod*.
	- Responsible for handling liveliness, readiness, and startup probes.
	- Responsible for *Mounting volumes* by reading pod configuration and creating respective directories on the host for the volume mount.
	- *Collecting and reporting Node and pod status via calls to the API server*.

> [!note] **Kubelet is also a controller** where it watches for pod changes and utilizes the node’s container runtime to pull images, run containers, etc.

- **[[KodeCloud - CKA - Static Pods | Static Pods]] are controlled by kubelet**, not the API server.
- Key points about kubelet:
	- Kubelet uses the **CRI (container runtime interface)** gRPC interface to talk to the *container runtime*.
	- Uses the **CSI (container storage interface)** gRPC to *configure block volumes*.
	- It uses the **CNI (container network interface)** plugin configured in the cluster to *allocate the pod IP address and set up any necessary network routes and firewall rules for the pod*.
- Diagram:
	- ![[attachments/Pasted image 20230515154756.png]]

#### Kube proxy
- Service in Kubernetes is a way to expose a set of pods internally or to external traffic.
	- When we *create the service object*, it gets a **virtual IP** assigned to it which is called *clusterIP*. 
	- It is *only accessible within the Kubernetes cluster*.
- **Whenever we create a service an endpoint object is also created**.
	- The *Endpoint object contains all the IP addresses* and *ports* of pod groups under a Service object.
		- We can get the endpoints using `k get endpoints`
			- ![[attachments/Pasted image 20230516073023.png]]
		- If we view the yaml config of a specific endpoint using `k get endpoint/<endpoint-name>` we see a list of IP addresses and ports
			- ![[attachments/Pasted image 20230516072359.png]]
	- The **endpoints controller** is responsible for maintaining a **list (mapping) of pod IP addresses** (endpoints).
	-  *Endpoints can be thought of as a lookup table* for Services to fetch the target IP addresses of Pods.
	- The *service only declares the selectors* but the information to which pods traffic should be forwarded is with the endpoints.
	- The *service controller* is responsible for *configuring endpoints to a service*.

> [!important]- **We CANNOT ping the ClusterIP/Service IP because it is a virtual IP**.
> - We can ping the Pod IP since it is an actual endpoint on the network.
> - Service IP is just a key in the IP table rules set by IP tables that gives routing instructions to the host kernel.

- Kube-proxy is a **daemon that runs on every node as a [[KodeCloud - CKA - Daemon Sets | daemon set]]**. 
	- It is a proxy component that **implements the Kubernetes Services concept for pods** i.e. single DNS for a set of pods with load balancing.
- When we expose pods using a Service (ClusterIP), *Kube-proxy creates network rules to send traffic to the backend pods* (endpoints) grouped under the Service object. 
	- Meaning, **all the load balancing, and service discovery are handled by the Kube proxy**.
- Kube-proxy uses any one of the following modes to create/update rules for routing traffic to pods behind a Service.
	- **IPTables**: It is the *default mode*.
		- IPtables allow us to *program how the Linux kernel should treat packets*.
		- In IPTables mode, the traffic is handled by *IPtable rules*.
		- In this mode, kube-proxy *chooses the backend pod **random** for load balancing*.
	- **IPVS**: For clusters with services exceeding 1000, *IPVS offers performance improvement*. 
		- It supports the a lot of load balancing algorithms.
- kube-proxy runs on each node of a Kubernetes cluster and **watches Service and Endpoints (and EndpointSlices) objects** and *accordingly updates the IP table routing rules* on its host nodes to allow communicating over Services.
	- ![[attachments/Pasted image 20230516105810.png]]
- Analogy:
	- `Deployment -> Replicaset -> Pod`
	- `Service -> EndpointSlice -> Endpoint`

#### Container Runtime
- Container runtime **runs on all the nodes in the Kubernetes cluster**. 
	- It is responsible for *pulling images from container registries*, *running containers*, allocating and isolating resources for containers, and managing the entire lifecycle of a container on a host.
- **Container Runtime Interface (CRI)** is a set of APIs that allows Kubernetes to *interact with different container runtimes*. 
	- It allows *different container runtimes to be used interchangeably with Kubernetes*.
- **Open Container Initiative (OCI)** is a set of *standards* for *container formats* and *runtimes*.

> [!note]- **kubelet agent is responsible for interacting with the container runtime using CRI APIs to manage the lifecycle of a container**. 
> It also gets all the container information from the container runtime and provides it to the control plane.

- *High level of how container runtimes (CRI-O) work in k8s*.
	- When there is a *new request for a pod from the API server*, the *kubelet talks to CRI-O daemon* to launch the required containers via Kubernetes Container Runtime Interface.
	- CRI-O checks and pulls the required container image from the configured container registry using containers/image library.
	- CRI-O then generates OCI runtime specification (JSON) for a container.
	- CRI-O then launches an OCI-compatible runtime (runc) to start the container process as per the runtime specification.
	- ![[attachments/Pasted image 20230515160953.png]]

## References
- [Kubernetes Architecture Explained [Comprehensive Guide] (devopscube.com)](https://devopscube.com/kubernetes-architecture-explained/)
- [Demystifying kube-proxy | Mayank Shah](https://mayankshah.dev/blog/demystifying-kube-proxy/)
	- Good read for understanding kube-proxy
- [How etcd works with and without Kubernetes (learnk8s.io)](https://learnk8s.io/etcd-kubernetes)
	- Good read for understanding etcd.