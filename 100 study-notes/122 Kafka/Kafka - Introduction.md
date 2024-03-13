---
created: 2024-03-13 11:28
updated: 2024-03-13 12:16
---
---
**Links**: [[122 Kafka Index]]

---
## Introduction
- Apache Kafka is used for **real time event streaming**.
- *Data is stored as a stream of events*.
- We use a producer to send events to a Kafka cluster and a consumer to read events from a Kafka cluster.
- A Kafka cluster is **made up of brokers**.
	- *Each broker is nothing but an individual node/machine/container*.  Each broker has its own disk (local storage).
	- *The brokers are networked together to act as a single Kafka cluster*.
- **Zookeeper is used as a distributed consensus manager in Kafka**. It is responsible for:
	- Cluster management.
	- Failure detection & recovery.
	- Store ACLs & secrets.
- Simple Kafka architecture:
	- ![[attachments/Pasted image 20240313114252.png]]
- *Producers and consumers are decoupled from each other*.
	- Slow Consumers do not affect Producers.
	- Add Consumers without affecting Producers and vice versa.
	- Failure of Consumer does not affect System.
- **A topic is a collection of sequence of messages/events in Kafka**.
	- Any number of producers can write to a topic and any number of consumers can consume from a topic. It is a many to many mapping.
	- We can have unlimited topics.
- **Each topic is broken down into pieces known as partitions**.
	- *These partitions are allocated to different brokers in the cluster*.
- Topic architecture
	- ![[attachments/Pasted image 20240313115319.png]]

> [!note]- **Every partition has strict ordering**. We can only add messages to the end of any partition of a topic.
> All the previous messages are immutable events.
> ![[attachments/Pasted image 20240313115951.png]]

- *Since a topic is broken down into multiple partitions we DON'T have a strict ordering of events across a topic*.
- Reading messages from Kafka doesn't destroy them. We can have multiple consumers consuming different events from a topic.
	- ![[attachments/Pasted image 20240313120133.png]]
- *A broker is responsible for managing multiple partitions*.
- **Each partition has a configurable number (default 3) of replicas**.
	- One of the 3 is called the leader and others are followers.
	- Producers is producing to the leader and its the job of the brokers to keep the follower replicas in sync with the leader.
	- ![[attachments/Pasted image 20240313120626.png]]
- Each event/message consists of headers (optional metadata), **key, value** and timestamp (set at the time of ingestion or can be set manually).

> [!question]- How does the producer know, which partition it should write to?
> - If there is *no key* then round robin is used to populated messages to partitions. *There is no ordering*.
> - If there is a key then `hash(key) % number_of_partitions`. **This means the same key will always be written to the same partitions**. This is *important for ordering* (since we know that messages in a partition are ordered).

- Consumers pull messages from a topic and *keep track of the last message using offset*.
	- *Offset of each consumer into each partition that it is responsible for is stored in topic known as consumer offsets*. This is useful because if a consumer disconnects, it will know where it was.
- Each consumer lives in a consumer group. This is how we scale our consumers.
	- ![[attachments/Pasted image 20240313121603.png]]

