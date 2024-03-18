---
created: 2024-03-18 11:44
updated: 2024-03-18 11:51
---
---
**Links**: [[122 Kafka Index]]

---
## Zookeeper
- Zookeeper manages brokers (keeps a list of them)
- Zookeeper helps in performing leader election for partitions.
- Zookeeper sends notifications to Kafka in case of changes (e.g. new topic, broker dies, broker comes up, delete topics, etc....)
- **Kafka 2.x CAN'T work without Zookeeper**.
- **Kafka 3.x can work without Zookeeper (KIP-500) - using Kafka Raft instead**.
- **Kafka 4.x will NOT have Zookeeper**.
- Zookeeper by design operates with an odd number of servers (1, 3, 5, 7).
	- Zookeeper has a leader (writes) the rest of the servers are followers (reads).
		- ![[attachments/Pasted image 20240318115109.png]]
- *Kafka clients should directly connect to Kafka brokers instead of connecting to zookeeper*.
