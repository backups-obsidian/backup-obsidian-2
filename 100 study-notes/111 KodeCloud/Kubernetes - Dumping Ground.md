---
created: 2023-05-05 14:50
updated: 2023-05-05 15:03
---
---
**Links**: [[111 KodeCloud Index]]

---
## Events
- We track metrics using Prometheus and Grafana.
- We track application logs using ELK or fluentd.
- k8s generates a lot of events related to deployment, scheduling etc.
- Events are a great source of information to understand what is currently happening in our cluster.
- To get the event of a particular resource we can use `describe`
	- `k describe <resource-type> <resource-name>`
- To get all the events we can use:
	- `k get events`
- Some important events:
	- `Crashloopbackoff`: A container within a Pod repeatedly *crashes immediately after starting up*. When this happens, Kubernetes will automatically restart the container to try to recover it, but if it *crashes again within a short period of time*, Kubernetes will give up and mark the container as "CrashLoopBackOff".
	- `Imagepullbackoff`: When a container within a Pod *fails to pull its container image* from the specified image repository. When this happens, Kubernetes will try to pull the image several times, and if it continues to fail, it will mark the container as "ImagePullBackOff".