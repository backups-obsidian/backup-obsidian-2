---
created: 2023-05-08 13:07
updated: 2023-05-08 13:07
---
---
**Links**: [[111 KodeCloud Index]]

---

## K8s APIs
- *Resource types are organized into API groups*, and the groups are versioned independently from each other
- The `/api` endpoint is already legacy and used only for core resources (pods, secrets, configmaps, etc.). 
	- A more modern and generic `/apis/<group-name>` endpoint is used for the rest of resources, including user-defined custom resources.
- ![[attachments/Pasted image 20230508125952.png]]