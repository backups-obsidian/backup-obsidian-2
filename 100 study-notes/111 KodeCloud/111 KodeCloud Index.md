---
created: 2022-09-13 16:17
updated: 2022-10-01 19:47
---
---
**Links**: [[../100 home| 100 Home]]

---
## DevOps Prerequisites 
[[KodeCloud - Package Management using yum]]
[[KodeCloud - Linux Services]]
[[KodeCloud - Networking]]
[[KodeCloud - Applications]]
[[KodeCloud - WebServers]]
[[KodeCloud - Databases]]
[[KodeCloud - SSL-TLS]]

## Kubernetes
[[KodeCloud - Kubernetes Beginner]]

[[KodeCloud - CKAD - Misc]]
[[KodeCloud - CKAD - Namespaces]]
[[KodeCloud - CKAD - Commands and Arguments]]
[[KodeCloud - CKAD - Environment Variables]]
[[KodeCloud - CKAD - Security Context]]
[[KodeCloud - CKAD - Resource Requirements]]
[[KodeCloud - CKAD - Service Accounts]]
[[KodeCloud - CKAD - Taints and Tolerations]]
[[KodeCloud - CKAD - Node Selectors and Node Affinity]]
[[KodeCloud - CKAD - Multi Container Pods]]
[[KodeCloud - CKAD - Probes]]
[[KodeCloud - CKAD - Logging]]
[[KodeCloud - CKAD - Labels, Selectors and Annotations]]
[[KodeCloud - CKAD - Jobs]]
[[KodeCloud - CKAD - Network Policies]]
[[KodeCloud - CKAD - Ingress]]

To Do: 
- [ ] Volume mounts inside pods and containers
	- In the below file what is being mounted to what location
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
    - name: app
      image: kodekloud/event-simulator
      volumeMounts:
        - mountPath: /log
          name: log-volume
    - name: sidecar
      image: kodekloud/filebeat-configured
      volumeMounts:
        - mountPath: /var/log/event-simulator/
          name: log-volume
  volumes:
    - name: log-volume
      hostPath:
		# directory location on host
        path: /var/log/webapp
		# this field is optional
        type: DirectoryOrCreate
```
- My hunch is that directory location on host is the directory location on pod.
- In Docker it is simple since we only have the host and the container. 

- Things to do
	- add readiness or liveness probes