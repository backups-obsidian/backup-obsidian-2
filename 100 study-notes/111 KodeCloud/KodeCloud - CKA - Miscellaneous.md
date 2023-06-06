---
created: 2023-05-30 15:00
updated: 2023-06-06 10:42
---
---
**Links**: [[111 KodeCloud Index]]

---
## High Availability
- For high availability we need multiple master nodes.
- Multiple master nodes means there will be multiple API servers.
	- *API servers can work in active active configuration*.
	- **We would need a load balancer to balance the requests sent to the API server**.
	- Diagram:
		- ![[attachments/Pasted image 20230530145709.png]]
- For the *controller manager and scheduler they CANNOT run in active active configuration* i.e. multiple instances must not run in parallel.
	- They run in an *active standby mode*.
	- To *achieve the active and standby node leader election process is used*.

## Finalizers & Owner References
- Finalizers are identified as *keys on resources that control the garbage collection process before deletion*. 
	- They are *used by controllers* to determine which cleanup operations should be performed.
	- They are a way to ensure that a *Kubernetes resource is NOT deleted until certain conditions are met*.
- **Dead finalizers are finalizer strings that the controller does not understand**, leading to difficulties in deletion. 
	- Understanding finalizers and their associated actions can aid in debugging deletion issues.
- Finalizers, present in an object's metadata, are a list of strings. 
- One use case for using finalizers is when there are dependent resources that should be deleted before deleting the main resource.
	- Finalizers can also be used to clean up resources before deletion.
- *The deletion process involves updating the object with a deletion timestamp, signaling that the object can only be read (except for removing finalizer keys)*.
- The state diagram illustrates the finalization process, showing that an *object remains in the "finalization" state until the finalizers are removed by controllers*.
	- ![[attachments/Pasted image 20230605164913.png]]
- **Owner references establish relationships between objects, enabling the deletion of entire trees of resources**. 
	- Common examples include pods referencing their replica sets.
	- Child objects are associated with parent objects, and deleting the parent triggers the deletion of all dependent objects.
- The cascade option allows the deletion of a parent without affecting its children.
	- Using `k delete --cascade=false` on parent objects orphans the child objects and removes their owner references.

### References
- [Clean Up Your Room! What Does It Mean to Delete Something in K8s - Aaron Alpar, Kasten - YouTube](https://www.youtube.com/watch?v=F7-ZxWwf4sY) 
	- Covers owner references too.
- [What are Kubernetes Finalizers? | Implementing Kubernetes finalizer for a resources. - YouTube](https://www.youtube.com/watch?v=3HlpWI-D3-M)

## JSON Path
- Find out what you want to extract using `k get pods -o json`.
- Provide the JSON path query: `k get pods -o jsonpath='{ .root.object[2]}'`
- Some examples of JSON path queries:
	- ![[attachments/Pasted image 20230531143625.png]]
	- ![[attachments/Pasted image 20230531143652.png]]
	- Using loops
		- ![[attachments/Pasted image 20230531143807.png]]

## General Tips
- When we run `k run pod --image=nginx` the container runtime is actually pulling images from `docker.io/library/nginx`.
	- Here *`nginx` is the image name*.
	- *`library` is the user/account name*. 
		- *`library` is the name of the default account where docker's official images are stored*.
	- `docker.io` is the default registry from where images are pulled.
		- Google's registry is `grc.io` where a lot of k8s related images are stored.
	- Diagram:
		- ![[attachments/Pasted image 20230524113731.png]]
- **Port Forwarding** to a particular pod:
	- `k port-forward <pod-name> <port-number>`

## FAQs
> [!question]- How to determine the resources required by each pod so that I can specify it in deployment definition file?
> - Install the metrics API server and run `k top pods`.