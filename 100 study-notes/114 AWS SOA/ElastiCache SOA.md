---
created: 2023-03-06 08:13
updated: 2023-03-06 08:29
---
---
**Links**: [[114 AWS SOA Index]]

---
## Redis Scaling
### Cluster Mode Disabled
- *Horizontal*:
	- Scale out/in by *adding/removing read replicas* (max. 5 replicas)
- *Vertical*:
	- *Scale up/down* to larger/smaller node type
	- ElastiCache **will internally create a new node group, then data replication and DNS update**.
		- ![[attachments/Pasted image 20230306081456.png]]

### Cluster Mode Enabled
- Two Modes:
	- **Online Scaling**: continue serving requests during the scaling process (*no downtime*, some degradation in performance)
	- **Offline Scaling**: *unable to serve requests* during the scaling process (backup and restore). 
		- Additional configurations supported (change node type, upgrade engine version, etc)
- *Horizontal*: (*Resharding and Shard Rebalancing*)
	- Resharding: scale out/in by adding/removing shards
	- Shard Rebalancing: equally distribute the keyspaces among the shards as possible
	- Supports **Online and Offline Scaling**
- *Vertical*: (*change read/write capacity*)
	- Scale up/down to larger/smaller node type
	- Supports **Online Scaling**

## Redis Metrics to Monitor
- **Evictions**: the *number of non-expired items the cache evicted* to allow space for new writes (*memory is overfilled*). Solution:
	- *Choose an eviction policy to evict expired items* (e.g., evict least recently used (*LRU*) items).
	- *Scale up to larger node type* (more memory) or scale out by adding more nodes.
- **CPUUtilisation**: monitor CPU utilisation for the entire host
	- Solution: *scale up to larger node type* (more memory) or scale out by adding more nodes.
- **SwapUsage**: should not exceed 50 MB
	- Solution: verify that you have configured *enough reserved memory*.
- **CurrConnections**: the number of *concurrent and active connections*
	- Solution: investigate application behaviour to address the issue
- DatabaseMemoryUsagePercentage: the percentage of memory utilisation
- NetworkBytesIn/Out & NetworkPacketsIn/Out
- ReplicationBytes: the volume of data being replicated
- *ReplicationLag*: how far behind the *replica is from the primary node*

## Memcached Scaling
- Horizontal:
	- Add/remove nodes from the cluster
	- **Auto-discovery** allow your app to find nodes
- Vertical:
	- Scale up/down to larger/smaller node type
	- *Scale up process*:
		- Create a *new cluster* with the new node type
		- *Update* your *application to use the new cluster's endpoints*
		- *Delete the old cluster*
		- ![[attachments/Pasted image 20230306082429.png]]
	- **Memcached clusters/nodes start out empty** since there is no replication.

## Memcached Auto Discovery
- Auto Discovery automatically identifies all of the nodes
- *All the cache nodes in the cluster maintain a list of metadata about all other nodes*.
- This is seamless from a client perspective

## Memcached Metrics
- Same as [[ElastiCache SOA#Redis Metrics to Monitor | Redis]]
- *FreeableMemory*: amount of *free memory on the host*.