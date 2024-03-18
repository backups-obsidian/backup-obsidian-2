---
created: 2024-03-13 11:28
updated: 2024-03-18 16:14
---
---
**Links**: [[122 Kafka Index]]

---
## Introduction
- Kafka has a distributed, resilient and fault tolerant architecture.
- *The latency is very less and kafka can be considered a real time system*.
- Apache Kafka is used for **real time event streaming**.
- *Data is stored as a stream of events*.
- We use a producer to send events to a Kafka cluster and a consumer to read events from a Kafka cluster.
- A Kafka cluster is **made up of brokers**.
	- *Each broker is nothing but an individual node/server/container*.
	- *The brokers are networked together to act as a single Kafka cluster*.
	- After connecting to any broker (called a bootstrap broker - returns a list of all the brokers), you will be connected to the entire cluster (Kafka clients have smart mechanics for that).
		- **Each broker knows about all brokers, topics and partitions (metadata)**.
- Simple Kafka architecture:
	- ![[attachments/Pasted image 20240313114252.png]]
- *Producers and consumers are decoupled from each other*.
	- Slow Consumers do not affect Producers.
	- Add Consumers without affecting Producers and vice versa.
	- Failure of Consumer does not affect System.

### Miscellaneous
- There is no correlation between the number of brokers and the number of partitions.
- *When the broker containing the leader partition dies then one of the follower partition is elected as the new leader*.
	- This is done automatically by Kafka.
	- ![[attachments/Pasted image 20240313135651.png]]
	- This can be configured globally or per topic.
- In general produces waits for **acknowledgement** when it sends the event to Kafka.
	- ![[attachments/Pasted image 20240313140448.png | Different types of ACKs]]
- **Different delivery guarantees**:
	- *At most once*: we might lose some data.
	- *At least once*: there might be duplicates.
	- *Exactly once*: no duplication or loss of data
	- Diagram: 
		- ![[attachments/Pasted image 20240313141828.png]]
- We can use Compacted topics if are interested in the most recent value of a key.
	- ![[attachments/Pasted image 20240313142626.png]]
- Security in Kafka:
	- Kafka supports Encryption in Transit.
	- Kafka supports Authentication and Authorization
	- No Encryption at Rest out of the box.

- Running Kafka locally:
	- Port 19092 is the Kafka port
	- Kafka CLI tools can be installed using `brew install kakfa`.
```bash fold title:"Using docker compose to install and run kafka locally"
curl -L https://releases.conduktor.io/quick-start -o docker-compose.yml && docker compose up -d --wait && echo "Conduktor started on http://localhost:8080"
```

- Example command: `kafka-topics --bootstrap-server localhost:19092 --list`