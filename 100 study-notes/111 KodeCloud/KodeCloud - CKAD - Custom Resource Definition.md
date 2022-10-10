---
created: 2022-09-15 21:09
updated: 2022-10-10 13:37
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
	


