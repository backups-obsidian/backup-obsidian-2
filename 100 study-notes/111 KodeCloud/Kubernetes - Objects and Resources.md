---
created: 2023-05-08 12:49
updated: 2023-05-08 12:59
---
---
**Links**: [[111 KodeCloud Index]]

---
## Objects and Resources
- In Kubernetes, **any item that a user creates and retains is referred to as an Object**.
	- These can include various items such as *namespaces*, *pods*, *deployments*, *volumes*, or *secrets*.
	- Any object you create, gets stored in etcd.
		- ![[attachments/Pasted image 20230508125118.png]]
- etcd stores all objects under the key named `/registry` directory. 
	- For instance, details regarding a pod called Nginx in the default namespace can be retrieved by searching for `/registry/pods/default/nginx`.
	- ![[attachments/Pasted image 20230508125256.png]]
- To create objects, you describe what you want (desired state) in a file using either YAML or JSON. 
	- It is called a *Object Specification* (pod, deployment, etc definition file).

> [!note] In Kubernetes, everything is accessed through APIs.

- To *create different types of objects* such as pods, namespaces, configmaps etc, *Kubernetes API server provides API endpoints*.
	- **These object-specific endpoints are called API resources or resources**. 
	- For example, the *API endpoint used to create a pod is referred to as a Pod resource*.
- In simpler terms, a *resource is a specific API URL* used to access an object, and they can be accessed through HTTP verbs such as GET, POST, and DELETE.
	- For instance, the `/api/v1/pods` resource can be used to retrieve a list of Pod objects, and an individual Pod object can be obtained from the ``/api/v1/namespaces/pods/`` resource.
- *Every Kubernetes resource has a **Group**, **Version** and **Kind** that uniquely identifies it*.
	- ![[attachments/Pasted image 20230508123513.png]]

> [!tip] *Resources* are like **blueprints or templates for Kubernetes objects**, while **objects are actual instances created from those blueprints** that are running in the Kubernetes cluster.

> [!important] When creating a Kubernetes object using kubectl, the **YAML specification is converted to JSON format** and sent to the Pod resource (Pod API endpoint).

## References
- [Kubernetes Objects Vs Resources Vs Custom Resource (devopscube.com)](https://devopscube.com/kubernetes-objects-resources/)