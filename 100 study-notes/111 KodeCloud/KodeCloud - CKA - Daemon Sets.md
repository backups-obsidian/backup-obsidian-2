---
---
---
**Links**: [[111 KodeCloud Index]]

---
## Daemon Sets
- Daemon sets are like replica sets in the sense that it *helps us run multiple instances of a pod* but it runs **one copy of pod on each node of our cluster**.
	- Whenever a new node is added to our cluster a replica of the pod is added to that node and when the node is removed the pod is removed.
- 