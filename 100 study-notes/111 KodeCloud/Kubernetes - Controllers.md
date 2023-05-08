---
created: 2023-05-08 08:21
updated: 2023-05-08 13:00
---
---
**Links**: [[111 KodeCloud Index]]

---
## Objects vs Resources

## K8s APIs
- *Resource types are organized into API groups*, and the groups are versioned independently from each other
- The `/api` endpoint is already legacy and used only for core resources (pods, secrets, configmaps, etc.). 
	- A more modern and generic `/apis/<group-name>` endpoint is used for the rest of resources, including user-defined custom resources.
- ![[attachments/Pasted image 20230508125952.png]]

## Controllers
- Some times we will run into situations where the k8s resources (pod, deployment, service etc) are too limiting or not precisely what we need.
	- For this purpose k8s supports **custom resource definitions (CRDs)**.
- Each resource in k8s (like pod, deployment, etc) is an endpoint on the k8s API.
- *Custom resources is a way of extending that API*.