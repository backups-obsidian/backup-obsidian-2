---
created: 2022-05-16 12:39
updated: 2022-06-07 15:17
---
---
**Links**: [[102 AWS DVA Index]]

---
## Extended Client
- *How to send large messages*? The size limit on messages is 256 KB.
- We can upload the large message to S3 and use a *pointer* to the large message (*small metadata message*) as a message to SQS.
	- ![[attachments/Pasted image 20220519203156.png]]

## Must Know API
- `CreateQueue(MessageRetentionPeriod)`, `DeleteQueue`
- `PurgeQueue`: delete all the messages in queue
- `SendMessage(Delay Seconds)`, `ReceiveMessage`, `DeleteMessage`
- `MaxNumberOfMessages`: default 1, **max 10** (for `ReceiveMessage` API)
	- To receive a *batch of messages*.
- `ReceiveMessageWaitTimeSeconds`: Long Polling
- `ChangeMessageVisibility`: change the message timeout
- Batch APIs for `SendMessage`, `DeleteMessage`, `ChangeMessageVisibility` helps **decrease your costs**.

## FIFO Advanced
### Message Deduplication
- De-duplication interval is **5 minutes**. This means that if you send the same message *twice within the 5 minutes* then the second message will be *refused*.
- Two de-duplication methods:
	- *Content-based deduplication*: will do a **SHA-256 hash** of the message body
		- ![[attachments/Pasted image 20220519204755.png]]
		- ![[attachments/Pasted image 20220519205238.png]]
	- Explicitly provide a **MessageDeduplicationID**

### Message Grouping
- If you specify the *same value of `MessageGroupID` in an SQS FIFO queue*, you can **only have one consumer**, and *all the messages are in order*.
- To get **ordering at the level of a subset of messages**, specify *different values for MessageGroupID*
	- ![[attachments/Pasted image 20220519205145.png]]
	- *Messages that share a common `MessageGroupID` will be in order within the group*
	- *Each Group ID can have a different consumer* (**parallel processing**)
	- **Ordering across groups is not guaranteed**
- This is useful when you don't need ordering of all the messages but want ordering of messages for a particular ID.

> [!important] You can have **as many consumers as MessageGroupID** for your SQS FIFO queues.