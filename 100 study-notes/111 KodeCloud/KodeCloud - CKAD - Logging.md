---
created: 2022-09-15 21:09
updated: 2022-09-26 13:40
---
---
**Links**: [[111 KodeCloud Index]]

---
## Logging
- We can view the `STDOUT` logs in docker using: `docker logs -f <container-id>`
	- Remember to start the container in detached mode.
	- `-f` is used to stream the logs live.
- In k8s if the container outputs the logs to `STDOUT` we can view it using: 
	- `k logs -f <pod-name>`
	- `-f` is used to stream the logs live.
- If we have **multiple containers in the pod then we must specify the name of container explicitly in the command** otherwise it would fail.
	- `k logs -f <pod-name> <container-name>`
	- ![[attachments/Pasted image 20220926133356.png]]