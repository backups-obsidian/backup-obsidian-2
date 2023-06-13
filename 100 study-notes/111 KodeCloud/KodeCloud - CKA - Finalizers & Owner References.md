---
---
---
**Links**: [[111 KodeCloud Index]]

---
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