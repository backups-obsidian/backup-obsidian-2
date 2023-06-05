---
created: 2023-05-30 15:00
updated: 2023-06-01 15:28
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

> [!important]- 
## FAQs
- How to determine the resources required by each pod so that I can specify it in deployment definition file?
	- Install the metrics API server and run `k top pods`.