---
created: 2022-08-30 23:33
updated: 2022-09-05 22:23
---
---
**Links**: [[110 Kubernetes Index]]

---
### Listing things
- `kubectl get node`
- `kubectl get node -o wide`: more info
- `kubectl get node -o yaml`: a lot of info in yaml format, we can also get it in json format.

> [!note]- We can use `jq` to get specific information from the nodes
```bash
kubectl get nodes -o json | jq ".items[] | {name:.metadata.name} + .status.capacity"
```

- We can list all available resource types by running `kubectl get`
- `kubectl get all`: get all the services

### Describing things
- `kubectl describe type/name`
- `kubectl explain type`: to view the definition of a resource type

### Running commands
- `kubectl exec busybox nslookup kubernetes.default`: run commands in containers
- `kubectl exec -it busybox /bin/sh`: getting into the pods

