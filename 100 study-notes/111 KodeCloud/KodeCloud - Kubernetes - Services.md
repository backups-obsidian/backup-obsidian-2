---
created: 2022-10-10 15:35
updated: 2023-05-16 07:47
---
---
**Links**: [[111 KodeCloud Index]]

---
## Services
- Different service types
	- ![[attachments/Pasted image 20220921221406.png]]
	- NodePort: Pods are made accessible on a port on the node.

> [!tip]+ TargetPort is the port of the pod to which the service forwards the request.
> In short TargetPort is the target for the service.
> *All the terms are from the view point of the service*.
> ![[attachments/Pasted image 20220921222726.png]]

- In a service definition file the *only mandatory field is port*. 
	- If you don't provide NodePort it is assigned by k8s
	- If you don't provide TargetPort it is assumed to be same as Port.
	- *Ports is an array and we can have multiple such port mappings*.

- Sample service file
```yaml
apiVersion: vl
kind: Service
metadata:
	name: myapp-service
	# we can also provide labels but they are not necessary
spec:
	type: NodePort
	ports:
	 - targetPort: 80
	   port: 80
	   nodePort: 30008
	# no matchLabels key inside selector like we had in ReplicaSet
	selector:
		app: myapp
```

- **Whenever we create a NodePort a ClusterIP is automatically created for us**.
	- ![[attachments/Pasted image 20230503091452.png]]

> [!note] The **selector labels in the service should match the labels of the pod**.
> ![[attachments/Pasted image 20230503090705.png]]
> If we are using a deployment then the pod definition and its labels will be under the `spec` section of the of deployment manifest.

- Service acts as a *load balancer with a random algorithm* with a SessionAffinity set to Yes.
- What happens when pods are distributed across nodes?
	- **We can use IP of any of the nodes**.
	- ![[attachments/Pasted image 20220921223309.png]]

- Use of ClusterIP
	- ![[attachments/Pasted image 20220921223928.png]]
	- Each layer can scale and move without impacting communication between various services.

- **Each service gets an IP and name assigned to it and that is the name that should be used by other pods to access the service**.
	- Application code which needs to communicate with other pods in a different service should also use this name.
	- ![[attachments/Pasted image 20220921225516.png]]
	- Instead of hardcoding we should be using environment variables.
- *ClusterIP is the default type*.

- Disadvantage of NodePort
	- Even if we have pods on only 2 out of 4 nodes they are accessible from the IP addresses of all the nodes.
	- ![[attachments/Pasted image 20220921224350.png]]

> [!note]- We can bootstrap a k8s cluster from scratch using `kubeadm`

## References
- Might read: [[../110 Kubernetes/Kubernetes - Services Deep Dive|Kubernetes - Services Deep Dive]]