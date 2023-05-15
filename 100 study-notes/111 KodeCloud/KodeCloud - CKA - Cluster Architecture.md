---
created: 2022-10-10 15:37
updated: 2023-05-15 15:49
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
	- It works in a leader-member fashion for high availability and to withstand node failures.
- etcd *stores all configurations, states, and metadata of Kubernetes objects* (pods, secrets, daemonsets, deployments, configmaps, statefulsets, etc).
- etcd allows a **client to subscribe to events using `Watch()` API** . 
	- Kubernetes *api-server uses the etcd’s watch functionality to track the change in the state of an object*.
- etcd stores all objects under the `/registry` directory key in key-value format. 
	- For example, information on a pod named Nginx in the default namespace can be found under `/registry/pods/default/nginx`.

> [!note] etcd it is the **only Statefulset component in the control plane**.

#### kube-scheduler
- kube-scheduler is responsible for **scheduling pods on worker nodes**.
- When we deploy a pod, we specify the pod requirements such as CPU, memory, affinity, taints or tolerations, priority, persistent volumes (PV),  etc. 
	- The *scheduler’s primary task is to identify the create request and choose the best node for a pod that satisfies the requirements*.
- High level overview of how scheduler works:
	- ![[attachments/Pasted image 20230515150722.png]]
- **Scheduler working**:
	- To choose the best node, the Kube-scheduler uses **filtering and scoring** operations.
	- In the **filtering phase**, the scheduler finds the best-suited nodes where the pod can be scheduled.
		-  For example, if there are five worker nodes with resource availability to run the pod, it selects all five nodes.
		- If It is a large cluster, let’s say 100 worker nodes, and the scheduler doesn’t iterate over all the nodes. There is a scheduler configuration parameter called `percentageOfNodesToScore`.
	- In the **scoring phase**, the scheduler *ranks the nodes by assigning a score to the filtered worker nodes*. 
		- The scheduler makes the scoring by calling *multiple scheduling plugins*. 
		- Finally, the *worker node with the highest rank will be selected for scheduling the pod*. 
		- If *all the nodes have the same rank, a node will be selected at random*.
	- Once the node is selected, the **scheduler creates a binding event in the API server**. 
		- Meaning an event to bind a pod and node.
- The scheduler has **two phases**. 
	- **Scheduling cycle** and the **Binding cycle**. 
	- Together it is called the *scheduling context*. 
	- The *scheduling cycle selects a worker node* and the *binding cycle applies that change to the cluster*.
- We **can create custom schedulers and run multiple schedulers in a cluster along with the native scheduler**. 
	- When we deploy a pod we can specify the custom scheduler in the pod manifest.
	- So the scheduling decisions will be taken based on the custom scheduler logic.
- The scheduler has a *pluggable scheduling framework*. 
	- Meaning, we can add our custom plugin to the scheduling workflow.

> [!note] kube-scheduler is a **controller** that listens to pod creation events in the API server.

#### Kube Controller Manager
- [[Kubernetes - Controllers | What is a Controller?]]
- **Kube controller manager** is a *component that manages all the Kubernetes controllers*. 
	- Kubernetes *resources/objects* like pods, namespaces, jobs, replicaset are *managed by respective controllers*. 
	- Also, the **kube scheduler is also a controller** managed by Kube controller manager.
- We can extend k8s with custom controllers associated with a custom resource definition.
 
> [!note]- **Controllers watch the kube-api server** and make required changes to achieve the desired state.

#### Cloud Controller Manager
- When kubernetes is deployed in cloud environments, the cloud controller manager **acts as a bridge between Cloud Platform APIs and the Kubernetes cluster**.
- Cloud controller integration allows Kubernetes cluster to provision cloud resources like instances (for nodes), *Load Balancers* (for services), and *Storage Volumes* (for persistent volumes).
	- ![[attachments/Pasted image 20230515153802.png]]
- Cloud Controller Manager contains a set of **cloud platform-specific controllers** that ensure the desired state of cloud-specific components (nodes, Loadbalancers, storage, etc).

### Worker Node Components
#### Kubelet
- Kubelet is an agent component that **runs on every node in the cluster**. 
	- It *DOES NOT run as a container* instead **runs as a daemon, managed by systemd**.
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
	- Kubelet uses the *CRI (container runtime interface)* gRPC interface to talk to the *container runtime*.
	- Uses the *CSI (container storage interface)* gRPC to *configure block volumes*.
	- It uses the *CNI* plugin configured in the cluster to *allocate the pod IP address and set up any necessary network routes and firewall rules for the pod*.
- Diagram:
	- ![[attachments/Pasted image 20230515154756.png]]

#### Kube proxy

## References
- [Kubernetes Architecture Explained [Comprehensive Guide] (devopscube.com)](https://devopscube.com/kubernetes-architecture-explained/)
