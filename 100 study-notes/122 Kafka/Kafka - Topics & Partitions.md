---
created: 2024-03-18 10:14
updated: 2024-03-18 12:32
---
---
**Links**: [[122 Kafka Index]]

---
## Topics & Partitions
- **A topic is a collection of sequence of messages/events in Kafka**.
	- *A topic can be thought of as a table* in a database (Kafka).
	- Any number of producers can write to a topic and any number of consumers can consume from a topic. It is a many to many mapping.
	- We can have unlimited topics.
	- Topics support any kind of message format.
	- *The sequence of messages/events in a topic is known as a data stream*. This is why Kafka is known as a data streaming platform.
- **Each topic is broken down into pieces known as partitions**.
	- *These partitions are allocated to different brokers in the cluster*.
		- ![[attachments/Pasted image 20240318110742.png]]
	- Each topic can have any number of partitions.
- Each message *within a partition gets an incremental id, called offset*.
	- Offsets only have meaning for a specific partition. Example offset 3 in partition 1 and offset 3 in partition 2 are different messages.
	- *Offsets are NOT re-used even if previous messages have been deleted*.
- Topic architecture
	- ![[attachments/Pasted image 20240313115319.png]]

> [!note]- **Every partition has strict ordering**. We can only add messages to the end of any partition of a topic.
> All the previous messages are immutable events and *CANNOT be deleted or updated*.
> ![[attachments/Pasted image 20240313115951.png]]

- **By default events/messages are kept for 1 week**.
- *Since a topic is broken down into multiple partitions we DON'T have a strict ordering of events across a topic*.
- Reading messages from Kafka doesn't destroy them. We can have multiple consumers consuming different events from a topic.
	- ![[attachments/Pasted image 20240313120133.png]]
- Each event/message consists of 
	- Headers (optional metadata - key value pair), 
	- **Key** - can be null
	- **Value**
	- Timestamp (set at the time of ingestion or can be set manually)
	- Compression type
	- *Partition + offset*

### Replication
- **Each partition has a configurable number (default 3) of replicas**.
	- One of the 3 is called the leader and others are followers.
	- *Producers can only send data to the broker that is leader of a partition*.
	- *Consumers by default will read from the leader broker for a partition*.
	- Diagram:
		- ![[attachments/Pasted image 20240313120626.png]]

> [!important]- As a rule, for a replication factor of N, you *can permanently lose up to N-1 brokers and still recover your data*. 
> For a topic replication factor of 3, topic data durability can withstand 2 brokers loss.

- **We CANNOT have a replication factor of greater than the number of brokers**.