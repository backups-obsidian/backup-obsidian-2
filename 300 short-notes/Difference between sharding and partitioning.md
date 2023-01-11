---
created: 2023-01-09 11:06
updated: 2023-01-10 10:55
---
---
**Links**: [[300 home]]

---
## Database sharding and partitioning
- Each database server is a shard and we say that the data is partitioned.
	- ![[attachments/Pasted image 20230110095814.png]]

> [!note]- *Shard* is at a *database level* and *partitioning* is at a *data level*.
> So we always shard a database and partition (split) the data.

- *We can have 5 mutually exclusive partitions and have them in one database server* (shard).
	- Partition is only operating on the data level.
	- We can have multiple database servers (shard) for the partitioned data.
	- So if we have 2 shards then we can split the 5 partitions across these 2 shards.
		- ![[attachments/Pasted image 20230110101247.png]]
- When the request comes first *we need to know in which partition does the data lie* and then *depending on which partition holds the data we would be forwarding the request to the corresponding shard*.
- Due to partitioning we can operate on small sets of data.

> [!important]+ If for some reason in the above example partition A is getting a lot of traffic and it is affecting the performance of partition C then we can move partition C to shard 2.
> This is also known as load balancing across partitions.
> This is the advantage of partitioning data. We can move it between shards.

- There are 2 types of partitioning:
	- *Horizontal partitioning*: operates at *row* or *table* level
	- *Vertical partitioning*: operates at *column* level

- Partitioning depends on a lot of things like the load, use case and the access pattern.
- Simple example to remember difference between sharding and partitioning:
	- ![[attachments/Pasted image 20230110104424.png]]
	- **Important to note that when we have sharding and no partitioning then it is equivalent to having a read replica**.
		- Read replica is for high reads
	- Example where you would partition the data but not shard it: Suppose you have 2 logical databases in one mysql server you can partition them.
	- **The case where we partition and shard the database is for high write throughput**.

> [!note]- So we shard the database when we want to handle large read and writes.

- Advantages and disadvantages of sharding and partitioning
	- ![[attachments/Pasted image 20230110105409.png]]

## References
- [Database Sharding and Partitioning - YouTube](https://www.youtube.com/watch?v=wXvljefXyEo) 