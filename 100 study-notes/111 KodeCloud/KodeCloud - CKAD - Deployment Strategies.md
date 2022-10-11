---
created: 2022-10-10 14:07
updated: 2022-10-10 14:23
---
---
**Links**: [[111 KodeCloud Index]]

---
## Deployment Strategies
- Deployment strategies:
	- ![[attachments/Pasted image 20221010140417.png]]
	- **Recreate**: Down time. All the older pods are destroyed first then the new ones are brought up.
	- **Rolling update**: One by one. *Default deployment strategy*.

### Blue/Green deployment
- We deploy the *new version (green)* first and then direct the traffic to it.
- This can be done easily by changing the label on the service to point to the pods of green deployment.
	- ![[attachments/Pasted image 20221010140756.png]]

### Canary deployment
- Only a small amount of traffic is sent to the new deployment.
- If everything looks good then we replace the older deployment with the new one.
- To implement in k8s we need to do 2 things:
	- ![[attachments/Pasted image 20221010142006.png]]
	- **Route traffic to both the versions**: This can be done by *using a common label selector for both the deployments*.
	- **Route only a small amount of traffic to version 2**: This can be done by reducing the number of pods in the version 2 deployment. 
		- This works since **k8s service distributes the traffic equally between all the pods**. It works on pods and not on deployments.

- Code example:
	- ![[attachments/Pasted image 20221010142131.png]]

- The main issue with doing canary deployment in k8s is that the traffic is governed by the number of pods in each deployment.
	- Service meshes like istio come with better control. 
	- With istio we can define the exact % of traffic to be distributed to each deployment.