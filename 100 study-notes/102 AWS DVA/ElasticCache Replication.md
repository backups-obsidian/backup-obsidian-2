---
created: 2022-04-17 15:43
updated: 2022-05-14 11:05
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/ElastiCache| ElastiCache]]

---
- There are 2 types of ElastiCache replication
## Cluster Mode: Disabled
- *One primary node* and upto *5 replicas*. There is **only one shard**.
	- ![[attachments/Pasted image 20220514105722.png]]
- **Asynchronous** Replication
- The *primary node is used for read/write* whereas the **other nodes are read-only**.
- One shard, **all nodes have all the data**
- *Multi-AZ enabled by default* for failover
- Useful for 
	- *Guarding against data* loss if node failure
	- Helpful to *scale read performance*

## Cluster Mode: Enabled
- **Data is partitioned across shards** which is helpful for **scaling writes**.
	- ![[attachments/Pasted image 20220514110232.png]]
- Each shard has a primary and up to 5 replica nodes (similar to cluster mode disabled)
- There is multi AZ capability.
- Up to **500 nodes per cluster**:
	- 500 shards with single master
	- 250 shards with master and replica. Since 250 x 2
	- 83 shards with one master and 5 replicas. Since 83 x 6 = 498
