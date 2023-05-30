---
created: 2023-05-30 11:41
updated: 2023-05-30 12:15
---
---
**Links**: [[111 KodeCloud Index]]

---
## Pause Container
- Pause containers are responsible for **holding the network namespace in a pod**.
	- They bootstrap the pod to establish all of the cgroups, reservations, and namespaces before its individual containers are created. 
	- The pause container image is always present, so the pod resource allocation happens instantaneously as containers are created.
- **IP addresses and other resources are attached to the pause container**.
- *Pause container is always created before the main (app) container*.
- Pause container is an *infrastructure container whose sole purpose is to hold all these namespaces*.
- If the **pause container is deleted**, Kubernetes **recreates the pod with a new IP address**.
- **Stopping the business application container does not trigger pod recreation, only the container itself is restarted**.
	- So the IP address remains the same.
- We can view the pause containers by going to a worker node and running:
	- `ctr -n k8s.io c ls`

> [!note] Pause container is like a parent container (PID 1) to all the other containers in the pod and is responsible for killing the zombie processes.

## References
- [Kubernetes Pause Containers - Certified Kubernetes Administrator - YouTube](https://www.youtube.com/watch?v=DGcNF7qO4d4)