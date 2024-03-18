---
created: 2024-03-18 10:46
updated: 2024-03-18 11:41
---
---
**Links**: [[122 Kafka Index]]

---
## Producers
- How does the producer know, which partition it should write to?
	- If there is *no key* then round robin is used to populated messages to partitions. *There is no ordering*.
	- If there is a key then `hash(key) % number_of_partitions`. 
		- **This means the same key will always be written to the same partitions**.
		- This is *important for ordering* (since we know that messages in a partition are ordered).
- **Producers know which partition it needs to write to and it is NOT something decided by Kafka**.
	- Hashing of the keys is done by the producer and not Kafka.
- Kafka **only accepts bytes as an input** from producers and **sends bytes out as an output** to consumers.
- Serialization is used by the producer to transform the object into bytes.
- Depending on the type of data in key and value, different serializers are used.
	- *For example if the key is an int then an `IntegerSerializer` is used and if the value is a string then a `StringSerializer` is used*.

## Consumers
- Consumers read data from a topic by using the **pull model**.
- Data is *read in order* from low to high offset *within each partitions*.
	- There is no ordering guarantee between partitions.
- Consumers **deserialize** the bytes into objects.

> [!note] The serialization / deserialization type MUST NOT change during a topic lifecycle.

### Consumer Groups
- Each consumer lives in a consumer group. This is how we scale our consumers.
	- ![[attachments/Pasted image 20240313121603.png]]
- If you have *more consumers than partitions, some consumers will be inactive*.
- We can have multiple consumer groups on the same topic.
	- ![[attachments/Pasted image 20240318105810.png]]
- **Kafka stores the offsets at which a consumer group has been reading**.
	- The offsets committed are in *Kafka topic* named `__consumer_offsets`.
	- When a consumer in a group has processed data received from Kafka, it should be periodically committing the offsets.
	- If a consumer dies, it will be able to read back from where it left off thanks to the committed consumer offsets!
- Delivery semantics for consumers:
	- **At least once** (usually preferred):
		- *Offsets are committed after the message is processed*.
		- If the processing goes wrong, the message will be read again.
		- This can result in *duplicate processing of messages*. Make sure your processing is idempotent (i.e. processing again the messages won't impact your systems)
	- **At most once**:
		- Offsets are *committed as soon as messages are received*.
		- *If the processing goes wrong, some messages will be lost*.
	- Exactly once
- Consumer group load balancing and rebalancing:
	- If a consumer group is subscribed to a topic then it tries to distribute the partitions equally among all the consumers in the consumer group.
	- Rebalancing needs to be done when a consumer is added or removed from the consumer group.
	- Diagram:
		- ![[attachments/Pasted image 20240313142258.png]]