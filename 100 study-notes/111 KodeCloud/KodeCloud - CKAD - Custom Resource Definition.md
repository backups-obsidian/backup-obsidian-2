---
created: 2022-09-15 21:09
updated: 2022-10-10 13:48
---
---
**Links**: [[111 KodeCloud Index]]

---
## Custom Resource Definition (CRD)
- For every resource we have controller. 
	- So when we create a resource it is stored in etcd.
	- Controller continuously watches the status of these objects to maintain the state as expected.
- Custom resource and custom controller
	- Flight ticket booking resource
	- We will need to create a flight ticket booking controller.
	- ![[attachments/Pasted image 20221010133555.png]]
- We need a CRD to tell k8s about the resource that we want to create.
	- ![[attachments/Pasted image 20221010133906.png]]
	- ![[attachments/Pasted image 20221010134049.png]]

> [!note]- Once we create a CRD we can create the custom resource.

- **We use CRD to create any kind of resource in k8s**.
- *Creating a CRD only allows us to create custom resources and store them on etcd*.
	- Since we don't have a controller yet.
	- A custom resource without a controller is just data sitting in etcd and does not do anything.

> [!important]+ So there are 2 steps for creating any custom resource in k8s
> - Defining a CRD
> - Writing a controller

- We write a custom controller using the go programming language.
	- Once we build the controller we compile it.
	- We specify the kubeconfig file that the controller can use to authenticate to the k8s api.
	- We may choose to package the custom controller as a docker image and run it as a pod or deployment.
