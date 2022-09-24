---
created: 2022-09-15 21:09
updated: 2022-09-23 23:32
---
---
**Links**: [[111 KodeCloud Index]]

---
## Docker Security
- **By default docker runs processes within containers as a root user**.
	- ![[attachments/Pasted image 20220923221912.png]]
	- So both within the container and outside the container the process is run as root.
- If we do not want docker to use the root user we can specify the user in the docker run command.
	- `docker run --user=1000 ubuntu sleep 3600`
	- ![[attachments/Pasted image 20220923222022.png]]

- Another way of enforcing user security is defining it in the docker image at the time of creation.
	- ![[attachments/Pasted image 20220923222127.png]]
- Docker implements are set of security features on the root user of the container to limit its abilities.
	- *The root user within the container is NOT like the root user on the host*.
- The root user is the most powerful user on the system.
	- **A root user can do anything and so does the process run by a root user**.
	- It has unrestricted access to the system.
	- Like creating or killing process, access control, setting group or user id and many more.
- These are *different capabilities* of the linux system that can be found at `/etc/include/linux/capability.h`
- In docker we can control and limit what capabilities are made available to the root user inside the container.
	- **By default docker runs the container with a limited set of capabilities**.
	- So the root user inside the container doesn't have the permission to reboot the host or perform operations that can disrupt the host or other containers running on the system.
- If we wish to override this behaviour and provide additional privileges than what is available we can use the `--cap-add` option in the docker run command
	- `docker run --cap-add MAC_ADMIN ubuntu`
- Similarly we can drop the privileges using the `--cap-drop` option
	- `docker run --cap-drop KILL ubuntu`

> [!danger]- If we wish to run the container with all privileges enabled we use the `--privileged` flag.
> `docker run --privileged ubuntu`

> [!important]+ What is the significance of user id 1000
> - Typically, Linux starts creating **normal users at UID 1000**. 
> - So *a user with UID 1000 is probably the first user ever created on that particular system* (beside *root, who always has UID 0*).

- For more information read: [[../104 Linux/Linux - UID & GID | UID & GID]]

## Kubernetes Security
- We can choose to *configure the security settings at the CONTAINER level OR at the POD level*.
- If we do it at the pod level it will carry to all the containers.
- If we do it at the container and the pod then the *settings on the container will override the settings of the pod*.

- Sample pod definition file with *Security Context at the POD level*:
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: web-pod
spec:
	securityContext:
		runAsUser: 1000
	containers:
	- name: ubuntu
	  image: ubuntu
	  command: ["sleep", "3600" ]
```

- Sample pod definition file with *Security Context at the Container level*:
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: web-pod
spec:
	containers:
	- name: ubuntu
	  image: ubuntu
	  command: ["sleep", "3600" ]
	  securityContext:
	    runAsUser: 1000
		capabilities: # list of capabilites to add to the container
		  add: ["MAC_ADMIN"]
```

> [!caution]- Capabilities are *only supported at the container level and NOT at the pod level*.
