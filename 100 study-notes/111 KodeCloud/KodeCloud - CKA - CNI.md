---
created: 2023-05-29 13:30
updated: 2023-05-30 08:54
---
---
**Links**: [[111 KodeCloud Index]]

---
## CNI (Container Networking Interface)
- CNI is a **plugin-based architecture with vendor-neutral specifications** and libraries for **creating network interfaces for Containers**.
	- A *CNI plugin is responsible for inserting a network interface into the container network namespace* (e.g., one end of a virtual ethernet (`veth`) pair) and making any *necessary changes on the host* (e.g., attaching the other end of the `veth` into a bridge). 
	- It then *assigns an IP address to the interface* and *sets up the routes* consistent with the IP Address Management section by invoking the appropriate IP Address Management (IPAM) plugin.
- It is *NOT specific to Kubernetes*. 
- *With CNI container networking can be standardized across container orchestration tools* like Kubernetes, Mesos, CloudFoundry, Podman, Docker, etc.
- When it comes to container networking, *companies might have different requirements* such as network isolation, security, encryption, etc. 
	- As container technology advanced, many network providers created CNI-based solutions for containers with a wide range of networking capabilities. 
	- We can call it as *CNI-Plugins*
	- This allows users to choose a networking solution that best fits their needs from different providers.
- *CNI focuses on the connectivity of container networks* and the *removal of allocated resources upon the termination of containers*.
	- The container/pod initially has no network interface. 
	- The container runtime calls the CNI plugin with verbs such as **ADD**, **DEL**, **CHECK** etc.
	- *ADD creates a new network interface for the container, and details of what is to be added are passed to CNI via JSON payload*.

> [!note] Docker DOES NOT follow CNI it has its own standard known as CNM (Container Network Model).

### Different types of CNI plugins
- CNI plug-ins are divided into three implementation modes: **Overlay**, **Routing**, and **Underlay**.
	- ![[attachments/Pasted image 20230530082152.png]]
- In **Overlay mode**, a *container is independent of the host’s IP address range*.
	- During cross-host communication, *tunnels are established between hosts* and all packets in the container CIDR block are encapsulated as packets exchanged between hosts in the underlying physical network. 
	- *This mode removes the dependency on the underlying network*.
- In **Routing mode**, *hosts and containers belong to different CIDR blocks*. 
	- *Cross-host communication is implemented through routing*. 
	- *No tunnels are established* between different hosts for packet encapsulation.
	- However, route interconnection *partially depends on the underlying network*. 
		- For example, a reachable route must exist from the underlying network to Layer 2.
- In **Underlay mode**, *containers and hosts are located at the same network layer and share the same position*. 
	- Network interconnection between containers depends on the underlying network.
	- Therefore, this mode is *highly dependent on the underlying capabilities*.


## CNI in k8s
> [!question]- How CNI works in k8s? (DOUBT since kubelet executes the binary)
> - **Kubelet interacts with container runtime to launch the scheduled pod**. 
> - The *CRI plugin which is part of the Container runtime interacts with the CNI plugin to configure the pod network*.
> ![[attachments/Pasted image 20230530081028.png]]

- **CNI is invoked by kubelet**.
	- When the container runtime expects to perform network operations on a container, it (like the *kubelet in the case of K8s*) *calls the CNI plugin with the desired command*.
- Diagram:
	- ![[attachments/Pasted image 20230530080156.png]]
- Weave CNI is installed as a daemonset. 
- By default CNI binaries are located at `/opt/cni/bin`
- To find the CNI being used: `ls /etc/cni/net.d/`
- Plugins for *IPAM: IP address management*:
	- `dhcp`, `host-local` & `static`

### Understanding network configuration format
- Reference diagram:
	- ![[attachments/Pasted image 20230530084853.png]]
- The file can be found at `/etc/cni/net.d`
- *JSON format*
- Order of the plugins array corresponds to the order in which the plugins will be called.
- The example shows 3 plugins: `bridge`, `tuning` and `myplugin1`.
	- `bridge` plugin calls one more plugin `dhcp`
- Each plugin can define a set of parameters. 

## References
- [Introduction to CNI | Kubernetes Networking - YouTube](https://www.youtube.com/watch?v=McIKOoPKgBk)